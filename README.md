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
   
![access key](https://github.com/user-attachments/assets/ff1e65d1-f321-4bd6-8ad4-5f07cc885cbb)

# Lets explain step by step the line code with before separating them into root modules for better understanding with respect to the objectives.
## Create a VPC:
  - Name: KCVPC
  - IPv4 CIDR block: 10.0.0.0/16

![vpc code](https://github.com/user-attachments/assets/4b26ac55-b8be-49c3-a959-ffc37bf5a5c2)

## create Subnets:
  - Public Subnet:
    Name: PublicSubnet
    IPv4 CIDR block: 10.0.1.0/24
    Availability Zone: Select any one from your region
    
![publicsubnet code](https://github.com/user-attachments/assets/0bda0651-1780-4869-ba82-aae1345ed611)
    
  - Private Subnet:
    Name: PrivateSubnet
    IPv4 CIDR block: 10.0.2.0/24
    Availability Zone: Select any one from your region (preferably the same as the Public Subnet for simplicity)
    
![private code pics](https://github.com/user-attachments/assets/669e9af2-58e5-4579-b632-2b0585c42b74)

## Configure an Internet Gateway (IGW):
  - Create and attach an IGW to KCVPC.

![internet gateway](https://github.com/user-attachments/assets/486b11b4-c11d-4099-a460-731090ce1fc4)

## Configure Route Tables:
  - Public Route Table:
    Name: PublicRouteTable
    Associate PublicSubnet with this route table.
    Add a route to the IGW (0.0.0.0/0 -> IGW).

![route table and association pics](https://github.com/user-attachments/assets/29e28322-f3c6-451a-b9b7-672b380c7e03)

  - Private Route Table:
    Name: PrivateRouteTable
    Associate PrivateSubnet with this route table.
    Ensure no direct route to the internet.

![private route table and ass pics ](https://github.com/user-attachments/assets/a067f53c-f7a9-4aca-abfa-1478cb162d92)


## Set Up Security Groups:
  - Create a Security Group for public instances (e.g., web servers):
  - Allow inbound HTTP (port 80) and HTTPS (port 443) traffic from anywhere (0.0.0.0/0).
  - Allow inbound SSH (port 22) traffic from a specific IP (e.g., your local IP). (https://www.whatismyip.com/)
  - Allow all outbound traffic.

![public sg pics ](https://github.com/user-attachments/assets/681bc4ea-1071-409b-9521-e3c3054c9969)

  - Create a Security Group for private instances (e.g., database servers):
  - Allow inbound traffic from the PublicSubnet on required ports (e.g., PostgreSQL port).
  - Allow all outbound traffic

![private sg pics ](https://github.com/user-attachments/assets/2290f829-c6f0-4b62-ae89-4d90b27f938d)

## Network ACLs:
### Configure NACLs for additional security on both subnets.
  - Public Subnet NACL:
  - Allow inbound HTTP, HTTPS, and SSH traffic.
  - Allow outbound traffic.
    
![public Acls pics](https://github.com/user-attachments/assets/149a6b9f-ca3a-465d-be56-384f9506b109)


  - Private Subnet NACL: Allow inbound traffic from the public subnet. 
  - Allow outbound traffic to the public subnet and internet.

![private acls pics ](https://github.com/user-attachments/assets/78c57685-43ed-44e2-b681-8fd4ceb30763)


![acls ass pics](https://github.com/user-attachments/assets/be2af91a-0190-436d-a2f9-c8194c0dc2d0)


## Creating keypair
  lets create a pair key locally and include it on the code for authorization and accessibility.
  same ssh-key will be used for both public and private instances for simplicity.  
  
![keypair pics](https://github.com/user-attachments/assets/35c578e6-46cc-4b42-834d-4f9424ad8162)

![ssh key pics ](https://github.com/user-attachments/assets/1998eff3-98db-4d55-a11a-6a60377a274c)



## public Instance.
  -  Write a script to install Nginx on your EC2 instance in the public subnet on deployment and postgresql client to acess the postresql server
    
![public instace pics](https://github.com/user-attachments/assets/7f7ccc32-cfd6-4451-a2f1-3272b041c82d)

    
## private Instance.

  - Write a script to install PostgreSQL on your EC2 instance in the public subnet on deployment

![private instance pics ](https://github.com/user-attachments/assets/50e31a38-8fb6-4215-93fe-2049ac8e4516)



# separate child modules for your resources and reference them in your root module for readability and re-usability of your code.
# Write a script to install Nginx on your EC2 instance in the public subnet on deployment
# Write a script to install PostgreSQL on your EC2 instance in the public subnet on deployment

# Deploying the code 

![modules pics ](https://github.com/user-attachments/assets/ffcb2702-9576-4b97-8189-af518bb6be12)


![modules initiated](https://github.com/user-attachments/assets/f2f12410-0523-445e-b47d-30703f1cf21a)


![terraform validate and plan](https://github.com/user-attachments/assets/2d2c1cab-d0d6-4aee-90fc-134bb3a07ef1)


![terraform apply](https://github.com/user-attachments/assets/a71a734c-b008-49fc-8bac-de878dbdeba4)



# On your aws account, verify: 
    - all the modules created 
    - copy the ssh login to ur instances

![Screenshot from 2024-07-23 00-56-10](https://github.com/user-attachments/assets/7cba3cda-4ff5-4c93-8fb3-fec420cc0637)


![Screenshot from 2024-07-23 00-57-38](https://github.com/user-attachments/assets/bd4998f9-45ce-41b7-89a1-eb22477881bc)

# ran terraform validate command to see verify if the codes are valid

![Screenshot from 2024-07-23 00-58-59](https://github.com/user-attachments/assets/0d6846ea-afa9-4c8b-baa1-d6a079929ad3)

![Screenshot from 2024-07-23 00-59-52](https://github.com/user-attachments/assets/d68ad961-2866-4f07-b2da-259571380fd8)

![Screenshot from 2024-07-23 01-01-00](https://github.com/user-attachments/assets/4fe8a3f0-18f7-4417-9a98-4ac4bd389192)

![Screenshot from 2024-07-23 01-02-35](https://github.com/user-attachments/assets/c1299630-e043-4e85-bd7f-616cdbe36978)

![Screenshot from 2024-07-23 01-04-28](https://github.com/user-attachments/assets/7f5b4ed3-a2dc-493e-a451-2b8d6dd0105e)

![Screenshot from 2024-07-23 01-05-09](https://github.com/user-attachments/assets/3db78dbf-1374-49dd-8d68-ba4a04f0c7de)

![Screenshot from 2024-07-23 01-06-49](https://github.com/user-attachments/assets/8f9b0cc6-5a11-4d5d-9190-1127b25f542d)


![Screenshot from 2024-07-23 01-07-52](https://github.com/user-attachments/assets/bef890e2-1de5-40ce-8530-9d8fd209f7c8)


## Verify that the public instance can be accessed via the internet and verify if service nginx is installed.

![ssh login](https://github.com/user-attachments/assets/b932e6bb-153e-4a97-8930-b445f809ab68)

![ping and verify](https://github.com/user-attachments/assets/49cb2f4d-e813-4f4c-bbcb-4df3ee38ac53)


## Verify that the instance can access the internet through the NAT Gateway and can communicate with the public instance.
### you have to allow all tracfic from public subnet (10.0.1.0/24) before you will be able to ssh or access the internet


![private-ssh log](https://github.com/user-attachments/assets/35c2c3e2-cf70-4ffa-9238-3ef020a9667e)

![private- ping and verifying ](https://github.com/user-attachments/assets/dd0445ac-0a4b-44c9-a975-c9389ccd4e12)

## verifying if the postgresql-client from the public instance can  access the progresql-server from the private instance
  - establish a database connection from the public instance into the postresql-server
    
![connect to database](https://github.com/user-attachments/assets/9f4bf4aa-18cb-4eab-b1f9-2d797a5db145)

![connect to database 2](https://github.com/user-attachments/assets/5399543e-9558-41e6-876a-fd821ff1c69f)


# Clean up resource on completion using terraform destroy

![terraform destroy](https://github.com/user-attachments/assets/72e4ad2a-fe23-43ad-8fdf-72cff2519db9)
![terraform destroy 2](https://github.com/user-attachments/assets/e015b618-04fe-4285-a294-ec7f97d49322)


# NOTE: the ssh key was not upload in this repo..

# Resources
    - https://registry.terraform.io/providers/hashicorp/aws/latest/docs

    - https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/user-data.html

    - https://www.youtube.com/watch?v=eEbstFkbl9w&ab_channel=AWSDevelopers














