# AWS Flipkart E-Commerce Application with RDS

A hands-on AWS deployment project for an e-commerce application using Amazon EC2, Amazon RDS (MySQL), Nginx, Application Load Balancer (ALB), Target Groups, ACM, Route 53, HTTPS, Security Groups, and email OTP functionality.

> **Reference repository:** The application code used for this learning project is based on the public repository:
> https://github.com/CloudTechDevOps/aws-ecommerce-Application-Multiple-services

This repository documents the deployment process, AWS configuration, testing, and supporting screenshots.

---

## Project Overview

The objective of this project was to deploy an e-commerce application on AWS, configure the application servers, connect the backend to Amazon RDS MySQL, expose the application through an Application Load Balancer, and secure access using HTTPS.

### AWS Services & Technologies

- Amazon EC2
- Amazon RDS MySQL
- Amazon VPC
- Security Groups
- Nginx
- Application Load Balancer
- Target Groups
- AWS Certificate Manager (ACM)
- Amazon Route 53
- HTTPS
- Node.js
- MySQL
- Linux

---

## Project Architecture

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
                     Target Group
                            |
                  +---------+---------+
                  |                   |
                  v                   v
             EC2 Server          EC2 Server
             Frontend             Backend/API
                                      |
                                      v
                                Amazon RDS
                                 MySQL DB
```

---

# 1. EC2 Servers

EC2 instances were launched for the application components.

The servers were used for frontend/backend deployment, Nginx configuration, and application testing.

### Screenshot

![EC2 Servers](docs/screenshots/EC2_SERVERS.jpeg)

---

# 2. Frontend Server

The frontend application was deployed and verified on the EC2 environment.

### Screenshot

![Frontend Server Running](docs/screenshots/FRONTEND_SERVER_RUNNING.jpeg)

---

# 3. Backend Server

The backend application was deployed on the backend server.

Example commands used during deployment:

```bash
cd backend
npm install
npm start
```

### Screenshot

![Backend Server Running](docs/screenshots/BACKEND_SERVER_RUNNING.jpeg)

---

# 4. API Verification

After starting the backend, the API was tested to verify that the backend service was running successfully.

### Screenshot

![API Running Successfully](docs/screenshots/API_RUNNING_SUCCESFULLY_MESSAGE.jpeg)

---

# 5. Amazon RDS MySQL Database

Amazon RDS was configured as the managed MySQL database for the application.

```text
Database Engine: MySQL
Port: 3306
```

### Screenshot

![RDS Database](docs/screenshots/DATABASE.jpeg)

---

# 6. RDS DB Subnet Group

A DB Subnet Group was configured for the RDS database.

### Screenshot

![DB Subnet Group](docs/screenshots/DB-SUBNET-GROUP.jpeg)

---

# 7. Application Load Balancer

An Application Load Balancer was configured to receive incoming application traffic and forward requests to the target group.

```text
Internet
   |
   v
Application Load Balancer
   |
   v
Target Group
   |
   +---- EC2
   |
   +---- EC2
```

### Screenshot

![Application Load Balancer](docs/screenshots/UPDATED_ALB_WITH_HTTPS.jpeg)

---

# 8. Target Group

A Target Group was configured for the EC2 application servers.

The target group allows the Application Load Balancer to route traffic to registered healthy instances.

### Screenshot

![Target Group](docs/screenshots/TARGET_GROUP.jpeg)

---

# 9. HTTPS with ACM

AWS Certificate Manager was used to configure an SSL/TLS certificate for HTTPS access.

The certificate was associated with the HTTPS listener of the Application Load Balancer.

### Screenshot

![Updated ALB with HTTPS](docs/screenshots/UPDATED_ALB_WITH_HTTPS.jpeg)

---

# 10. Route 53

Amazon Route 53 was configured to route the application domain to the Application Load Balancer.

```text
User
 |
 v
Route 53
 |
 v
Application Load Balancer
 |
 v
Application Servers
```

### Screenshot

![Route 53 Website](docs/screenshots/ROUTE53_WEBSITE.jpeg)

---

# 11. Application Through ALB

The website was tested through the Application Load Balancer to verify that traffic was reaching the application correctly.

### Screenshot

![Website Through ALB](docs/screenshots/CHECKING_WEBSITE_THROUGH_ALB.jpeg)

---

# 12. User Account Creation

The registration functionality was tested by creating an account on the deployed application.

### Screenshot

![Created an Account](docs/screenshots/CREATED_AN_ACCOUNT.jpeg)

---

# 13. Email OTP

The email OTP functionality was tested during the application flow.

### Screenshot

![Getting OTP on Mail](docs/screenshots/GETTING_OTP_ON_MAIL.jpeg)

---

# 14. Product Added to Cart

The product/cart functionality was tested after deployment.

### Screenshot

![Product on Cart](docs/screenshots/PRODUCT_ON_CART.jpeg)

---

# 15. Successful Order Email

The successful order flow was tested and the corresponding email notification was verified.

### Screenshot

![Order Successful Mail](docs/screenshots/ORDER_SUCCESSFULL_MAIL.jpeg)

---

# 16. Verify Database After Purchase

After completing a purchase through the website, the database was checked to verify that the application data was stored successfully in RDS MySQL.

```text
Website
   |
   v
Backend API
   |
   v
Amazon RDS MySQL
   |
   v
Order / Application Data
```

### Screenshot

![Checking Database After Buying](docs/screenshots/CHECKING_DATABASES_AFTER_BUYING_ON_WEBSITE.jpeg)

---

# 17. Security

The project uses AWS security controls such as:

- Security Groups
- Controlled inbound and outbound traffic
- HTTPS
- AWS Certificate Manager
- Route 53
- RDS MySQL
- Separate application/database access

Sensitive information such as passwords, private keys, database credentials, and secrets are not included in this repository.

---

# 18. Testing Performed

The deployed application was tested for:

- EC2 server availability
- Frontend server
- Backend server
- API response
- RDS database
- Target Group
- Application Load Balancer
- HTTPS
- Route 53 DNS
- User registration
- Email OTP
- Product/cart functionality
- Order flow
- Database updates
- Order email notification

---

# Technologies Used

| Category | Technologies |
|---|---|
| Cloud | AWS |
| Compute | Amazon EC2 |
| Database | Amazon RDS MySQL |
| Load Balancing | Application Load Balancer |
| Target Routing | Target Groups |
| DNS | Amazon Route 53 |
| SSL/TLS | AWS Certificate Manager |
| Web Server | Nginx |
| Networking | Amazon VPC |
| Security | Security Groups |
| Backend | Node.js |
| Database | MySQL |
| Operating System | Linux |
| Version Control | Git & GitHub |

---

# What I Learned

Through this project, I gained hands-on experience with:

- Deploying applications on Amazon EC2
- Working with AWS networking
- Configuring application servers
- Installing and configuring Nginx
- Deploying frontend and backend applications
- Creating Amazon RDS MySQL
- Connecting an application to RDS
- Configuring Target Groups
- Configuring an Application Load Balancer
- Setting up HTTPS using ACM
- Configuring Route 53
- Testing application functionality
- Verifying application data in RDS
- Troubleshooting AWS application connectivity

---

# Repository Structure

```text
AWS_PROJECT_FLIPKART_APPLICATION_WITH_RDS/
│
├── README.md
│
└── docs/
    └── screenshots/
        ├── API_RUNNING_SUCCESFULLY_MESSAGE.jpeg
        ├── BACKEND_SERVER_RUNNING.jpeg
        ├── CHECKING_DATABASES_AFTER_BUYING_ON_WEBSITE.jpeg
        ├── CHECKING_WEBSITE_THROUGH_ALB.jpeg
        ├── CREATED_AN_ACCOUNT.jpeg
        ├── DATABASE.jpeg
        ├── DB-SUBNET-GROUP.jpeg
        ├── EC2_SERVERS.jpeg
        ├── FRONTEND_SERVER_RUNNING.jpeg
        ├── GETTING_OTP_ON_MAIL.jpeg
        ├── ORDER_SUCCESSFULL_MAIL.jpeg
        ├── PRODUCT_ON_CART.jpeg
        ├── ROUTE53_WEBSITE.jpeg
        ├── TARGET_GROUP.jpeg
        └── UPDATED_ALB_WITH_HTTPS.jpeg
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
Amazon EC2
     +
Amazon RDS MySQL
     +
Application Load Balancer
     +
Target Groups
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

A hands-on AWS deployment project demonstrating how multiple AWS services can work together to deploy, expose, secure, and test an e-commerce application.
