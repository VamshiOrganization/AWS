- [<~ Back to CloudFormation](../../cloudFormation.md)
- [<~ ec2-docker-app-image-with-mysql-db](ec2-docker-app-image-with-mysql-db.md)
- [<~ order-service-production](order-service-production.yaml)
#### Remaining properties in this ec2-docker-app-image-with-mysql-db.md
## 1. Explanation of NEW Parameters & Properties

Compared to the previous single-EC2 template, this architecture sets up a production-ready, highly available infrastructure utilizing VPC, Auto Scaling, Application Load Balancer (ALB), Amazon RDS, and API Gateway.

### New Parameters

-   **`VpcCidr`**: Sets the overall IPv4 address block for your isolated virtual network (Default: `10.0.0.0/16` = 65,536 private/public IPs).
    
-   **`HealthCheckPath`**: Path the Load Balancer pings to check if the app is healthy (e.g., `/` or `/actuator/health`).
    
-   **`DBName` / `DBMasterUsername` / `DBMasterPassword`**: Configurations for the managed Amazon RDS database. Note that RDS rejects `root` as a master username, so `admin` is used instead.
    
-   **`MinLength: 8`**: Validates that the database password parameter contains at least 8 characters.
    
-   **`AsgMinSize` / `AsgMaxSize` / `AsgDesiredCapacity`**: Controls the capacity range for auto-scaling instance counts (1 minimum, 2 maximum, starting with 1).
    
-   **`ApiStageName`**: Defines the path environment stage for the API Gateway endpoint (Defaults to `v1`).
    

### New Networking Resources

-   **`AWS::EC2::VPC`**: Creates a dedicated, isolated virtual network in your AWS account.
    
    -   **`EnableDnsSupport` & `EnableDnsHostnames`**: Allows instances within the VPC to resolve public/private domain names (required for RDS connectivity).
        
-   **`AWS::EC2::InternetGateway` & `VPCGatewayAttachment`**: Attaches an internet gateway to your VPC, allowing resources inside public subnets to connect to the internet.
    
-   **`AWS::EC2::Subnet` (`PublicSubnetA` & `PublicSubnetB`)**: Splits your VPC network across two distinct Availability Zones (AZs) for high availability.
    
    -   **`!Cidr` / `!Select` / `!GetAZs`**: Dynamically calculates subnet IP ranges (`10.0.0.0/24`, `10.0.1.0/24`) and assigns them to active Availability Zones in whatever AWS region you deploy into.
        
    -   **`MapPublicIpOnLaunch: true`**: Automatically assigns public IPs to instances launched in these subnets.
        
-   **`AWS::EC2::RouteTable` / `Route` / `SubnetRouteTableAssociation`**: Routes outbound traffic (`0.0.0.0/0`) from both subnets through the Internet Gateway.
    

### New Security Group Architecture

Instead of one open Security Group, this template uses a **chained, multi-tier security model**:

-   **`AlbSecurityGroup`**: Permits public HTTP (`Port 80`) traffic from anywhere on the internet (`0.0.0.0/0`).
    
-   **`AppSecurityGroup`**: Restricts incoming traffic on `HostPort` (`8083`) so it **ONLY accepts requests originating from `AlbSecurityGroup`** (`SourceSecurityGroupId`). Internet traffic cannot bypass the load balancer to reach the EC2 instances directly.
    
-   **`DbSecurityGroup`**: Restricts MySQL (`3306`) access so **ONLY instances carrying `AppSecurityGroup`** can communicate with the database.
    

### New Database Resource (`AWS::RDS::DBInstance`)

-   **`AWS::RDS::DBSubnetGroup`**: Tells RDS which subnets it is allowed to use across multiple AZs.
    
-   **`Engine: mysql` & `EngineVersion: '8.0'`**: Launches a fully managed MySQL 8.0 database instance.
    
-   **`DBInstanceClass: db.t3.micro` & `AllocatedStorage: '20'`**: Allocates AWS Free Tier eligible database compute and 20GB of General Purpose SSD (`gp2`) storage.
    
-   **`PubliclyAccessible: false`**: Keeps the database endpoint private so it cannot be reached from the public internet.
    
-   **`DeletionPolicy: Snapshot`**: Ensures that if this CloudFormation stack is ever deleted, AWS will take a final database backup snapshot before removing the database.
    

### New Compute & Auto-Scaling Resources

-   **`AWS::EC2::LaunchTemplate`**: Replaces standard inline EC2 instance properties. Defines instance type, security groups, AMI, and `UserData` as a reusable template for scaling.
    
-   **`AWS::AutoScaling::AutoScalingGroup`**: Manages the lifecycle of EC2 instances dynamically.
    
    -   **`DependsOn: OrderDB`**: Guarantees CloudFormation will wait for the database to complete creation _before_ launching app instances.
        
    -   **`TargetGroupARNs`**: Automatically registers newly launched EC2 instances with the Application Load Balancer.
        
    -   **`HealthCheckType: ELB`**: If the ALB marks an instance unhealthy, the ASG automatically terminates it and launches a fresh one.
        
-   **`AWS::AutoScaling::ScalingPolicy`**: Adds target-tracking scaling. Automatically spins up a second EC2 instance when average CPU utilization across the group exceeds `60.0%`.
    

### New Load Balancing & API Gateway Resources

-   **`AWS::ElasticLoadBalancingV2::LoadBalancer`**: An HTTP/HTTPS Application Load Balancer (ALB) distributed across both public subnets to distribute incoming user traffic.
    
-   **`AWS::ElasticLoadBalancingV2::TargetGroup`**: Groups your application instances together and performs periodic health checks against `HealthCheckPath`.
    
-   **`AWS::ElasticLoadBalancingV2::Listener`**: Listens on `Port 80` of the ALB and forwards traffic directly to the target group.
    
-   **`AWS::ApiGatewayV2::VpcLink`**: A secure tunnel connecting API Gateway to private subnets/ALBs inside your VPC.
    
-   **`AWS::ApiGatewayV2::Api` / `Integration` / `Route` / `Stage`**: Sets up an HTTP API fronting the ALB. Route `'ANY /{proxy+}'` captures all incoming web routes and proxies them safely through the VPC Link to the Load Balancer.
    

## 2. In-Depth Breakdown of the `UserData` Code

In this template, notice that **MySQL is no longer running inside Docker on the EC2 instance**. Instead, MySQL runs on a separate managed RDS database instance, making the startup script much simpler and cleaner.

Bash

```
#!/bin/bash -xe
dnf update -y
dnf install -y docker
systemctl enable docker
systemctl start docker
usermod -aG docker ec2-user

docker pull ${DockerImage}
docker run -d --name app --restart unless-stopped \
  -p ${HostPort}:${ContainerPort} \
  -e SPRING_DATASOURCE_URL=jdbc:mysql://${OrderDB.Endpoint.Address}:3306/${DBName} \
  -e SPRING_DATASOURCE_USERNAME=${DBMasterUsername} \
  -e SPRING_DATASOURCE_PASSWORD=${DBMasterPassword} \
  ${DockerImage}

```

### Step-by-Step Execution:

1.  **`#!/bin/bash -xe`**:
    
    -   Runs the script in the Bash shell.
        
    -   `-x` outputs every executed command line into `/var/log/cloud-init-output.log` for easy troubleshooting.
        
    -   `-e` halts execution instantly if any single command returns a non-zero (failure) exit code.
        
2.  **Docker Installation & Enablement**:
    
    -   `dnf update -y`: Updates installed packages to the latest OS release.
        
    -   `dnf install -y docker`: Installs Docker Engine via Amazon Linux 2023 package manager.
        
    -   `systemctl enable docker` & `systemctl start docker`: Registers Docker as a system service on boot and starts the Docker daemon immediately.
        
    -   `usermod -aG docker ec2-user`: Adds the default SSH user (`ec2-user`) to the `docker` security group.
        
3.  **`docker pull ${DockerImage}`**:
    
    -   Fetches the application container image (e.g., `vamshivoore/spring-order-service:latest`) directly from Docker Hub onto the instance.
        
4.  **`docker run -d --name app ...`**:
    
    -   **`-d`**: Runs container in background/detached mode.
        
    -   **`--name app`**: Gives the running container a readable name.
        
    -   **`--restart unless-stopped`**: Configures Docker to automatically restart the application container if it crashes or if the host EC2 instance reboots.
        
    -   **`-p ${HostPort}:${ContainerPort}`**: Maps traffic arriving at port `8083` on the host server into port `8080` inside the container.
        
5.  **Dynamic RDS Database Linking (`-e SPRING_DATASOURCE_*`)**:
    
    -   **`${OrderDB.Endpoint.Address}`**: This is an implicit `!GetAtt` string substitution. CloudFormation dynamically injects the generated internal domain endpoint of the managed Amazon RDS database instance (e.g., `orderdb.c123456789.ap-south-2.rds.amazonaws.com`).
        
    -   **`-e SPRING_DATASOURCE_URL=jdbc:mysql://${OrderDB.Endpoint.Address}:3306/${DBName}`**: Constructs the JDBC connection string pointing directly to the managed RDS instance over port 3306.
        
    -   **`-e SPRING_DATASOURCE_USERNAME=${DBMasterUsername}` & `SPRING_DATASOURCE_PASSWORD=${DBMasterPassword}`**: Injects database login credentials as environment variables into Spring Boot.
