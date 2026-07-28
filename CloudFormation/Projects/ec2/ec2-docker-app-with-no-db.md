[~ Back to CloudFormation](../../cloudFormation.md)

### Parameters Section

-   **`AWSTemplateFormatVersion`**: Specifies the version of the CloudFormation template structure being used (always `2010-09-09`).
    
-   **`Description`**: Provides a brief summary of what the template creates when deployed.
    
-   **`Parameters`**: Defines inputs you can customize when launching the stack without changing the template code.
    
-   **`Type`**: Enforces the data type (e.g., `String`, `Number`, `AWS::EC2::VPC::Id`) for user inputs.
    
-   **`Default`**: Sets a pre-populated value for the parameter if you don't enter one manually.
    
-   **`AllowedValues`**: Limits the parameter input to a specific set of allowed options.
    
-   **`KeyPairName`**: Captures the name of your existing SSH key pair to allow login to the instance.
    
-   **`VpcId`**: Identifies the Virtual Private Cloud (VPC) network where the resources will be launched.
    
-   **`SubnetId`**: Specifies the particular subnet within your VPC where the EC2 instance resides.
    
-   **`InstanceType`**: Sets the virtual hardware configuration (CPU, memory) for the EC2 instance.
    
-   **`DockerImage`**: Specifies the Docker repository and tag for the image you want to pull and run.
    
-   **`ContainerPort`**: Defines the internal network port your application listens on inside the container.
    
-   **`HostPort`**: Defines the public-facing port on the EC2 instance that maps to your application.
    
-   **`EnvVars`**: Passes environment variable key-value pairs into the application container at runtime.
    
-   **`SSHLocationCIDR`**: Restricts SSH access (port 22) to a specific IP address range for security.
    

### Resources Section

-   **`Resources`**: Declares the actual AWS components (EC2, Security Groups) that CloudFormation will create.
    
-   **`ServiceSecurityGroup`**: Creates a virtual firewall to control inbound and outbound network traffic.
    
-   **`Type: AWS::EC2::SecurityGroup`**: Specifies that this resource is an EC2 Security Group.
    
-   **`GroupDescription`**: Briefly describes the purpose of the security group.
    
-   **`SecurityGroupIngress`**: Defines the incoming traffic rules, including permitted protocols, ports, and IP ranges.
    
-   **`IpProtocol`**: Sets the networking protocol allowed by the rule (usually `tcp`).
    
-   **`FromPort` / `ToPort`**: Specifies the starting and ending network port range allowed for incoming traffic.
    
-   **`CidrIp`**: Defines the range of IPv4 addresses allowed to access the specified ports.
    
-   **`ServiceInstance`**: Defines the virtual server (EC2 instance) being provisioned.
    
-   **`Type: AWS::EC2::Instance`**: Specifies that this resource is an Amazon EC2 compute instance.
    
-   **`KeyName`**: Attaches your specified SSH key pair to the instance for remote login.
    
-   **`SecurityGroupIds`**: Links the created Security Group to this EC2 instance to enforce firewall rules.
    
-   **`ImageId`**: References the Amazon Machine Image (AMI) ID used to boot the instance OS.
    
-   **`UserData`**: Runs shell scripts automatically on instance startup to install Docker and start your container.
    
-   **`Fn::Base64`**: A built-in function that converts startup scripts into base64 format required by EC2.
    
-   **`Tags` / `Key` / `Value`**: Assigns metadata labels (like a name) to help organize and search for resources in AWS.
    

### Outputs Section

-   **`Outputs`**: Displays useful values (like IP addresses or URLs) after the stack finishes creating.
    
-   **`InstanceId`**: Displays the unique ID of the newly created EC2 instance.
    
-   **`PublicIP`**: Displays the auto-assigned public IPv4 address of the server.
    
-   **`ServiceURL`**: Generates a clickable web address combining the instance domain name and host port.
    
-   **`Value`**: Defines the data returned by an output, using functions like `!Ref` or `!GetAtt`.
    
-   **`!Ref`**: Fetches the physical ID or value of a parameter or resource.
    
-   **`!GetAtt`**: Retrieves a specific attribute from a resource (such as an EC2 instance's public IP).
    
-   **`!Sub`**: Substitutes variables into a string at runtime.
