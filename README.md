# using Terraform to step up Virtual Private Cloud (VPC)
## Objective
1. Using Terraform, design and set up a Virtual Private Cloud (VPC) with both public and private subnets. Implement routing, security groups, and network access control lists (NACLs) to ensure proper communication and security within the VPC and an Ubuntu EC2 instance in each subnet. Work in the AWS EU-West-1 (Ireland) region.
2. Create separate child modules for your resources and reference them in your root module for readability and re-usability of your code.
3. Write a script to install Nginx on your EC2 instance in the public subnet on deployment
4. Write a script to install PostgreSQL on your EC2 instance in the public subnet on deployment
5. Clean up resource on completion using terraform destroy

# firstly: create an IAM user, generated an Accesskey and apply AministratorAccess to the user

![IAM user](https://github.com/user-attachments/assets/282ed91d-5f6d-4fe3-ba79-a08c95e13474)

## configure aws configure
### aws configuration commands is listed below

 - AWS Access Key ID [None]: YOUR_ACCESS_KEY_ID
 - AWS Secret Access Key [None]: YOUR_SECRET_ACCESS_KEY
 - Default region name [None]: us-west-2
 - Default output format [None]: json

---------access key pics




# Lets explain step by step the line code with before separating them into root modules for better understanding with respect to the objectives.
## Create a VPC:
  - Name: KCVPC
  - IPv4 CIDR block: 10.0.0.0/16

--------vpc code pics 


## create Subnets:
  - Public Subnet:
    Name: PublicSubnet
    IPv4 CIDR block: 10.0.1.0/24
    Availability Zone: Select any one from your region
    
public code pics
    
  - Private Subnet:
    Name: PrivateSubnet
    IPv4 CIDR block: 10.0.2.0/24
    Availability Zone: Select any one from your region (preferably the same as the Public Subnet for simplicity)

--------------private code pics.

## Configure an Internet Gateway (IGW):
  - Create and attach an IGW to KCVPC.

--------------internet gateway pics 

## Configure Route Tables:
  - Public Route Table:
    Name: PublicRouteTable
    Associate PublicSubnet with this route table.
    Add a route to the IGW (0.0.0.0/0 -> IGW).

------route table and association pics

  - Private Route Table:
    Name: PrivateRouteTable
    Associate PrivateSubnet with this route table.
    Ensure no direct route to the internet.

-------- private route table and ass pics

## Set Up Security Groups:
  - Create a Security Group for public instances (e.g., web servers):
  - Allow inbound HTTP (port 80) and HTTPS (port 443) traffic from anywhere (0.0.0.0/0).
  - Allow inbound SSH (port 22) traffic from a specific IP (e.g., your local IP). (https://www.whatismyip.com/)
  - Allow all outbound traffic.

    ---------public sg pics

  - Create a Security Group for private instances (e.g., database servers):
  - Allow inbound traffic from the PublicSubnet on required ports (e.g., PostgreSQL port).
  - Allow all outbound traffic

-------------private sg pics 

## Network ACLs:
### Configure NACLs for additional security on both subnets.
  - Public Subnet NACL:
  - Allow inbound HTTP, HTTPS, and SSH traffic.
  - Allow outbound traffic.

------------- public Acls pics 

  - Private Subnet NACL: Allow inbound traffic from the public subnet. 
  - Allow outbound traffic to the public subnet and internet.

-------------- private acls pics 
------------------acls ass pics 

## Creating keypair
  lets create a pair key locally and include it on the code for authorization and accessibility.
  
------------keypair pics
------------ssh key pics 


## public Instance.
  -  Write a script to install Nginx on your EC2 instance in the public subnet on deployment and postgresql client to acess the postresql server
    
-------- public instace pics 
    
## private Instance.

  - Write a script to install PostgreSQL on your EC2 instance in the public subnet on deployment

---------- private instance pics 



# Create separate child modules for your resources and reference them in your root module for readability and re-usability of your code.
# Write a script to install Nginx on your EC2 instance in the public subnet on deployment
# Write a script to install PostgreSQL on your EC2 instance in the public subnet on deployment

# Deploying the code 

--------------modules pics 

-------------modules initiated

-------------terraform validate

------------terraform apply


# on your aws account copy the ssh login to ur instances

## Verify that the public instance can be accessed via the internet and verity if service nginx is installed.

----- ssh login
---- ping and verify

## Verify that the instance can access the internet through the NAT Gateway and can communicate with the public instance.
### you have to allow all tracfic from public subnet (10.0.1.0/24) before you will be able to ssh or access the internet


----- private ssh log
------private pinging and verifying 

## verifying if the postgresql-client from the public instance  and access the progresql-server from the private instance
  - establish a database connection from the public instance into the postresql-server
    
---------------------connect to database 
---------------------connect to database2

  ![image](https://github.com/user-attachments/assets/12027ec6-cb4f-4ec3-87cd-6333c694cb5b)

# Clean up resource on completion using terraform destroy

--------terraform destroy 
--------terraform destroy 2













