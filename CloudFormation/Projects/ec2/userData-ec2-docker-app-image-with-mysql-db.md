- [<~ Back to CloudFormation](../../cloudFormation.md)
- [<~ ec2-docker-app-with-mysql-db yaml](ec2-docker-app-image-with-mysql-db.yaml)
- [<~ ec2-docker-app-with-mysql-db](ec2-docker-app-image-with-mysql-db.md)

## In-Depth Breakdown of the `UserData` Script

When the EC2 instance boots up, CloudFormation executes this bash script as the `root` user to automate the entire environment setup.

### Phase 1: Installing & Starting Docker

Bash

```
#!/bin/bash -xe
dnf update -y
dnf install -y docker
systemctl enable docker
systemctl start docker
usermod -aG docker ec2-user

```

-   **`#!/bin/bash -xe`**: Tells Linux to run this script using the Bash shell. `-x` prints every command to the log (`/var/log/cloud-init-output.log`) as it runs, and `-e` tells the script to exit immediately if any command fails.
    
-   **`dnf update -y`**: Updates all pre-installed OS packages to the latest security versions.
    
-   **`dnf install -y docker`**: Uses the Amazon Linux 2023 package manager (`dnf`) to download and install Docker.
    
-   **`systemctl enable docker` & `start docker`**: Enables Docker to start automatically if the server reboots, and immediately launches the Docker engine daemon.
    
-   **`usermod -aG docker ec2-user`**: Grants the default `ec2-user` permission to run Docker commands without needing `sudo`.
    

### Phase 2: Creating the Private Container Network

Bash

```
docker network create app-network

```

-   **`docker network create app-network`**: Creates an isolated virtual bridge network inside Docker.
    
-   Containers connected to `app-network` can talk to each other directly using their `--name` as a hostname (e.g., your Spring Boot app can reach MySQL simply by addressing `mysql-db`).
    

### Phase 3: Launching the MySQL Container

Bash

```
docker run -d --name mysql-db \
  --network app-network \
  -e MYSQL_ROOT_PASSWORD=${MySQLRootPassword} \
  -e MYSQL_DATABASE=${MySQLDatabase} \
  --restart unless-stopped \
  ${MySQLImage}

```

-   **`-d`**: Runs the MySQL container in detached (background) mode.
    
-   **`--name mysql-db`**: Names the container `mysql-db` so the app container can locate it by name on the network.
    
-   **`--network app-network`**: Connects this database container to the shared Docker network created in Phase 2.
    
-   **`-e MYSQL_ROOT_PASSWORD=...` & `-e MYSQL_DATABASE=...`**: Passes environment variables into the official MySQL container to initialize the root password and create the default database upon startup.
    
-   **`--restart unless-stopped`**: Ensures Docker automatically restarts this container if it crashes or if the EC2 instance restarts.
    
-   **Notice what's missing:** There is **no `-p 3306:3306`**. The database is deliberately kept off the host network so it cannot be accessed from the public internet.
    

### Phase 4: Waiting for MySQL Readiness

Bash

```
echo "Waiting for MySQL to be ready..."
for i in $(seq 1 30); do
  if docker exec mysql-db mysqladmin ping -uroot -p${MySQLRootPassword} --silent; then
    echo "MySQL is ready."
    break
  fi
  echo "MySQL not ready yet, retrying in 5s... ($i/30)"
  sleep 5
done

```

-   **Why this is necessary:** Simply starting a database container doesn't mean it's ready to accept connections. MySQL initialization takes 15–30 seconds to set up internal system tables. If your Spring Boot application starts before MySQL is completely ready, the application will throw a connection error and crash.
    
-   **`for i in $(seq 1 30)`**: Runs a loop up to 30 times.
    
-   **`docker exec mysql-db mysqladmin ping ...`**: Executes a health check inside the running MySQL container to ask, _"Are you ready to accept database queries?"_
    
-   **`break`**: Once MySQL replies successfully, the loop stops immediately and moves to the next phase.
    
-   **`sleep 5`**: If MySQL isn't ready yet, it pauses for 5 seconds before checking again (allowing up to 150 seconds total waiting time).
    

### Phase 5: Formatting Application Environment Variables

Bash

```
RAW_ENV="${EnvVars}"
ENV_FLAGS=""
for pair in $(echo "$RAW_ENV" | tr ',' ' '); do
  ENV_FLAGS="$ENV_FLAGS -e $pair"
done

```

-   **`RAW_ENV="${EnvVars}"`**: Takes the comma-separated string from your CloudFormation parameter (`SPRING_DATASOURCE_URL=...,SPRING_DATASOURCE_USERNAME=...`).
    
-   **`tr ',' ' '`**: Converts commas into spaces so bash can loop through each variable.
    
-   **`ENV_FLAGS="$ENV_FLAGS -e $pair"`**: Builds a single formatted string that converts your inputs into Docker flag format (i.e., `-e KEY1=VAL1 -e KEY2=VAL2`).
    

### Phase 6: Pulling and Starting the Spring Boot App

Bash

```
docker pull ${DockerImage}
docker run -d --name app \
  --network app-network \
  --restart unless-stopped \
  -p ${HostPort}:${ContainerPort} \
  $ENV_FLAGS \
  ${DockerImage}

```

-   **`docker pull ...`**: Downloads the specified Docker image from Docker Hub.
    
-   **`--network app-network`**: Places the application on the **exact same** virtual network as `mysql-db`.
    
-   **`-p ${HostPort}:${ContainerPort}`**: Maps incoming traffic on the server's public port (`8083`) to the port the app listens on internally (`8080`).
    
-   **`$ENV_FLAGS`**: Injects the formatted environment variables (DB URL, username, password) into the container so Spring Boot knows where to connect.
