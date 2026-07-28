- [<~ Back to CloudFormation](../../cloudFormation.md)
- [<~ ec2-docker-app-with-no-db](ec2-docker-app-with-no-db.yaml)

#### Remaining properties in this ec2-docker-app-with-no-db.yaml 

- [<~ userdata](userData-ec2-docker-app-image-with-mysql-db.md)

## 1. Explanation of NEW Properties & Values

Compared to your previous file, here are the newly added parameters and options:

### New Parameters

-   **`MySQLImage`**: Specifies the Docker Hub image and tag for the database container (Defaults to `mysql:8.0`).
    
-   **`MySQLDatabase`**: Sets the name of the database to be created automatically on startup (Defaults to `order_db`).
    
-   **`MySQLRootPassword`**: Specifies the password for the MySQL `root` user (Defaults to `root`).
    
-   **`NoEcho: true`**: A CloudFormation property applied to sensitive parameters. It masks the input value (displays `*****`) in the AWS Management Console and CLI outputs so passwords aren't shown in plain text.
    

### Modified Parameter Values

-   **`HostPort`**: Updated to default to `8083`.
    
-   **`EnvVars`**: Pre-filled with database connection strings: `SPRING_DATASOURCE_URL=jdbc:mysql://mysql-db:3306/order_db?createDatabaseIfNotExist=true,SPRING_DATASOURCE_USERNAME=root,SPRING_DATASOURCE_PASSWORD=root`
    
    -   **Key takeaway:** `mysql-db` is used as the host name instead of `localhost` or an IP address.
    
