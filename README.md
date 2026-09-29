# AWS Flipkart E-Commerce Application with RDS

A hands-on AWS deployment project for an e-commerce application using Amazon EC2, Amazon RDS (MySQL), Nginx, Application Load Balancer (ALB), Target Groups, ACM, Route 53, Security Groups, HTTPS, and email OTP functionality.

> **Reference:** The application code used for this learning project is based on the public repository:
> https://github.com/CloudTechDevOps/aws-ecommerce-Application-Multiple-services
>
> This repository documents my deployment process, AWS configuration, testing, and screenshots.

---

## Project Overview

The goal of this project was to deploy an e-commerce application on AWS and connect the application backend to an Amazon RDS MySQL database.

### AWS Services & Technologies Used

- Amazon EC2
- Amazon RDS MySQL
- Amazon VPC
- Public and Private Subnets
- Security Groups
- Internet Gateway
- Route Tables
- Nginx
- Application Load Balancer
- Target Groups
- AWS Certificate Manager (ACM)
- Amazon Route 53
- HTTPS
- Node.js
- MySQL
- Email OTP functionality

---

## Architecture

```text
                         Internet
                            |
                            v
                       Route 53
                            |
                            v
                    HTTPS / ACM
                            |
                            v
                Application Load Balancer
                            |
                    +-------+-------+
                    |               |
                    v               v
               Target Group    Target Group
                    |               |
                    v               v
               EC2 Server      EC2 Server
               Frontend        Backend/API
                                    |
                                    v
                              Amazon RDS
                               MySQL DB
```

---

# 1. AWS VPC Configuration

The application infrastructure was created inside an Amazon VPC.

Example VPC CIDR:

```text
10.0.0.0/16
```

The VPC provides the isolated networking environment required for the EC2 instances and database.

### Screenshot

![VPC Configuration](docs/screenshots/VPC.png)

---

# 2. Subnet Configuration

Subnets were created inside the VPC to organize the application infrastructure.

Example structure:

```text
VPC
|
+-- Public Subnet
|
+-- Public Subnet
|
+-- Private Subnet
|
+-- Private Subnet
```

### Screenshot

![Subnet Configuration](docs/screenshots/SUBNETS.png)

---

# 3. Internet Gateway

An Internet Gateway was attached to the VPC to provide internet connectivity for resources in public subnets.

### Screenshot

![Internet Gateway](docs/screenshots/INTERNET_GATEWAY.png)

---

# 4. Route Table

A route table was configured for the public subnet.

Example:

```text
Destination     Target

0.0.0.0/0       Internet Gateway
```

### Screenshot

![Route Table](docs/screenshots/ROUTE_TABLE.png)

---

# 5. Security Groups

Security Groups were configured to control inbound and outbound traffic.

Typical ports used in this project:

```text
22    -> SSH
80    -> HTTP
443   -> HTTPS
3306  -> MySQL
```

The database security group was configured to allow MySQL traffic from the required application/server security group.

### Screenshot

![Security Groups](docs/screenshots/SECURITY_GROUPS.png)

---

# 6. EC2 Instances

EC2 instances were launched for the application components.

The servers were used for the frontend/backend application deployment and Nginx configuration.

### Screenshot

![EC2 Servers](docs/screenshots/EC2_SERVERS.png)

---

# 7. Connect to EC2

After launching the EC2 instance, I connected to the server using SSH.

Example:

```bash
ssh -i your-key.pem ec2-user@YOUR_EC2_PUBLIC_IP
```

Private keys and sensitive credentials are not included in this repository.

### Screenshot

![EC2 Connection](docs/screenshots/EC2_CONNECTION.png)

---

# 8. Install Node.js and Required Packages

The required packages were installed on the application server.

```bash
sudo yum update -y
sudo yum install git -y
sudo yum install nodejs npm -y
```

Verify:

```bash
node --version
npm --version
```

### Screenshot

![Node Installation](docs/screenshots/NODE_INSTALLATION.png)

---

# 9. Install and Configure Nginx

Nginx was installed and configured as the web server/reverse proxy.

Install Nginx:

```bash
sudo yum install nginx -y
```

Start Nginx:

```bash
sudo systemctl start nginx
```

Enable Nginx:

```bash
sudo systemctl enable nginx
```

Check status:

```bash
sudo systemctl status nginx
```

### Screenshot

![Nginx Running](docs/screenshots/NGINX_RUNNING.png)

---

# 10. Frontend Application Deployment

The frontend application was deployed on the EC2 server.

Example:

```bash
git clone <repository-url>
cd <application-directory>
npm install
```

The application was then configured and started according to the project requirements.

### Screenshot

![Frontend Server Running](docs/screenshots/FRONTEND_SERVER_RUNNING.png)

---

# 11. Backend Application Deployment

The backend application was deployed on the backend EC2 server.

```bash
cd backend
npm install
npm start
```

### Screenshot

![Backend Server Running](docs/screenshots/BACKEND_SERVER_RUNNING.png)

---

# 12. API Verification

After starting the backend application, the API endpoint was tested to verify that the backend was responding successfully.

### Screenshot

![API Running Successfully](docs/screenshots/API_RUNNING_SUCCESFULLY_MESSAGE.png)

---

# 13. Amazon RDS MySQL

Amazon RDS was created as the managed MySQL database for the application.

```text
Database Engine: MySQL
Port: 3306
```

### Screenshot

![RDS Database](docs/screenshots/DATABASE.png)

---

# 14. DB Subnet Group

A DB Subnet Group was configured for the RDS database using the required subnets.

### Screenshot

![DB Subnet Group](docs/screenshots/DB-SUBNET-GROUP.png)

---

# 15. RDS Security Group

The RDS security group was configured so that the application server could communicate with MySQL on port 3306.

```text
Application Server
        |
        | TCP 3306
        v
    RDS MySQL
```

### Screenshot

![RDS Security Group](docs/screenshots/RDS_SECURITY_GROUP.png)

---

# 16. Connect Backend to RDS

The backend application was configured using the RDS MySQL endpoint.

Example:

```text
DB_HOST=your-rds-endpoint
DB_PORT=3306
DB_USER=your-db-user
DB_PASSWORD=your-db-password
DB_NAME=your-database
```

Actual database credentials are not included in this repository.

### Screenshot

![Database Connection](docs/screenshots/DATABASE_CONNECTION.png)

---

# 17. Application Load Balancer

An Application Load Balancer was created to receive incoming application traffic and forward it to the configured target groups.

```text
             Application Load Balancer
                         |
                 +-------+-------+
                 |               |
                 v               v
              EC2-1           EC2-2
```

### Screenshot

![Application Load Balancer](docs/screenshots/ALB.png)

---

# 18. Target Groups

Target Groups were created for the EC2 instances.

The ALB forwards requests to healthy registered targets.

```text
ALB
 |
 v
Target Group
 |
 +-- EC2 Instance 1
 |
 +-- EC2 Instance 2
```

### Screenshot

![Target Group](docs/screenshots/TARGET_GROUP.png)

---

# 19. ALB Listener

The Application Load Balancer listener was configured for application traffic.

Typical configuration:

```text
HTTP  -> Port 80
HTTPS -> Port 443
```

### Screenshot

![ALB Listener](docs/screenshots/ALB_LISTENER.png)

---

# 20. AWS Certificate Manager

AWS Certificate Manager was used to create an SSL/TLS certificate for the domain.

The certificate was associated with the HTTPS listener of the Application Load Balancer.

### Screenshot

![ACM Certificate](docs/screenshots/ACM_CERTIFICATE.png)

---

# 21. Route 53

Amazon Route 53 was configured for DNS management.

The domain was configured to route traffic to the Application Load Balancer.

```text
User
 |
 v
Domain
 |
 v
Route 53
 |
 v
Application Load Balancer
```

### Screenshot

![Route 53](docs/screenshots/ROUTE53.png)

---

# 22. HTTPS Configuration

HTTPS was configured using AWS Certificate Manager and the Application Load Balancer.

```text
Route 53
   |
   v
Application Load Balancer
   |
   v
ACM Certificate
   |
   v
HTTPS : 443
```

### Screenshot

![HTTPS Configuration](docs/screenshots/HTTPS.png)

---

# 23. Test Website Through ALB

After configuring the ALB and Target Group, the application was tested through the ALB DNS name.

```text
http://ALB-DNS-NAME
```

### Screenshot

![Website Through ALB](docs/screenshots/CHECKING_WEBSITE_THROUGH_ALB.png)

---

# 24. Access Website Through Domain

After configuring Route 53 and HTTPS, the application was accessed through the configured domain.

```text
https://your-domain.com
```

### Screenshot

![Website](docs/screenshots/WEBSITE.png)

---

# 25. Create User Account

The user registration functionality was tested by creating an account on the application.

### Screenshot

![Account Created](docs/screenshots/CREATED_AN_ACCOUNT.png)

---

# 26. Test Application Functionality

The deployed application was tested for its main functionality.

Tests included:

```text
User Registration
User Login
Product Browsing
Product Selection
Cart Functionality
Purchase Flow
Database Updates
```

### Screenshot

![Application Testing](docs/screenshots/APPLICATION_TESTING.png)

---

# 27. Email OTP Functionality

The email OTP functionality was tested as part of the application authentication/verification flow.

### Screenshot

![Email OTP](docs/screenshots/GETTING_OTP_ON_MAIL.png)

---

# 28. Verify Data in RDS

After performing an operation such as purchasing an item from the website, the RDS MySQL database was checked to verify that the application data was stored successfully.

```text
Website
   |
   v
Backend API
   |
   v
RDS MySQL
   |
   v
Data Stored
```

### Screenshot

![Checking Database After Buying](docs/screenshots/CHECKING_DATABASE_AFTER_BUYING_ON_WEBSITE.png)

---

# Security

The project uses AWS security controls including:

- Security Groups
- Private database connectivity
- HTTPS
- ACM SSL/TLS certificate
- Controlled application ports
- Route 53 DNS
- RDS MySQL

Sensitive information such as passwords, private keys, database credentials, and other secrets are not stored in this repository.

---

# Testing Checklist

- EC2 connectivity
- Nginx service
- Frontend server
- Backend server
- API response
- RDS connectivity
- MySQL database operations
- ALB routing
- Target Group health
- Route 53 DNS resolution
- HTTPS access
- User registration
- Login functionality
- Product functionality
- Purchase functionality
- Database updates
- Email OTP functionality

---

# Technologies Used

| Category | Technologies |
|---|---|
| Cloud | AWS |
| Compute | Amazon EC2 |
| Database | Amazon RDS MySQL |
| Load Balancing | Application Load Balancer |
| DNS | Amazon Route 53 |
| SSL/TLS | AWS Certificate Manager |
| Web Server | Nginx |
| Networking | VPC, Subnets, Route Tables, Internet Gateway |
| Security | Security Groups |
| Backend | Node.js |
| Database | MySQL |
| Operating System | Linux |
| Version Control | Git & GitHub |

---

# What I Learned

Through this project, I gained hands-on experience with:

- Deploying applications on Amazon EC2
- Creating and configuring AWS VPC networking
- Working with public and private subnets
- Configuring Security Groups
- Installing and configuring Nginx
- Deploying frontend and backend applications
- Creating and configuring Amazon RDS MySQL
- Connecting an application to RDS
- Creating Application Load Balancers
- Configuring Target Groups
- Configuring Route 53
- Creating SSL certificates using ACM
- Configuring HTTPS
- Troubleshooting application connectivity
- Verifying application data inside RDS
- Understanding how multiple AWS services work together in a real deployment

---

# Repository Structure

```text
AWS_PROJECT_FLIPKART_APPLICATION_WITH_RDS/
|
+-- README.md
|
+-- docs/
    |
    +-- screenshots/
        |
        +-- VPC.png
        +-- SUBNETS.png
        +-- INTERNET_GATEWAY.png
        +-- ROUTE_TABLE.png
        +-- SECURITY_GROUPS.png
        +-- EC2_SERVERS.png
        +-- EC2_CONNECTION.png
        +-- NODE_INSTALLATION.png
        +-- NGINX_RUNNING.png
        +-- FRONTEND_SERVER_RUNNING.png
        +-- BACKEND_SERVER_RUNNING.png
        +-- API_RUNNING_SUCCESFULLY_MESSAGE.png
        +-- DATABASE.png
        +-- DB-SUBNET-GROUP.png
        +-- RDS_SECURITY_GROUP.png
        +-- DATABASE_CONNECTION.png
        +-- ALB.png
        +-- TARGET_GROUP.png
        +-- ALB_LISTENER.png
        +-- ACM_CERTIFICATE.png
        +-- ROUTE53.png
        +-- HTTPS.png
        +-- CHECKING_WEBSITE_THROUGH_ALB.png
        +-- WEBSITE.png
        +-- CREATED_AN_ACCOUNT.png
        +-- APPLICATION_TESTING.png
        +-- GETTING_OTP_ON_MAIL.png
        +-- CHECKING_DATABASE_AFTER_BUYING_ON_WEBSITE.png
```

---

# Reference Repository

The application code used as the basis for this learning project was referenced from:

https://github.com/CloudTechDevOps/aws-ecommerce-Application-Multiple-services

This repository focuses on documenting my AWS deployment, infrastructure configuration, testing process, and screenshots.

---

# Author

**Venkata Subbarao**

AWS Cloud / DevOps Enthusiast

GitHub:  
https://github.com/VENKATASUBBARAO13

LinkedIn:  
https://www.linkedin.com/in/ventrapragada-venkata-subbarao

---

## Project Summary

```text
AWS EC2
    +
Amazon RDS MySQL
    +
Application Load Balancer
    +
Target Groups
    +
VPC Networking
    +
Security Groups
    +
Nginx
    +
Route 53
    +
ACM
    +
HTTPS
    +
Linux
    +
Node.js
```

A complete hands-on AWS deployment project demonstrating how multiple AWS services can be integrated to deploy, secure, expose, and test an e-commerce application.
