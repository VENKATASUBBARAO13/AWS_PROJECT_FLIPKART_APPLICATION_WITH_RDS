# AWS_PROJECT_FLIPKART_APPLICATION_WITH_RDS

A hands-on AWS deployment project for an e-commerce application using **Amazon EC2, Amazon RDS (MySQL), Nginx, Application Load Balancer (ALB), ACM, Route 53, Security Groups, Target Groups and email OTP functionality**.

> **Learning/reference source:** The application code used for this project is based on the public repository:
> https://github.com/CloudTechDevOps/aws-ecomerce-Application-Multiple-services
>
> This repository is organized as a step-by-step deployment guide so that another learner can follow the setup, verify each stage, and compare the result with the screenshots.

---

## Project Flow

```text
                         Route 53
                            |
                            v
                    +----------------+
                    |   ALB HTTPS    |
                    |    :443        |
                    +--------+-------+
                             |
                       Target Group
                             |
                             v
                    +----------------+
                    | Frontend EC2   |
                    | Nginx :80      |
                    +--------+-------+
                             |
                       /api proxy
                             |
                             v
                    +----------------+
                    | Backend EC2    |
                    | Flask :5000    |
                    +--------+-------+
                             |
                             v
                    +----------------+
                    | Amazon RDS     |
                    | MySQL :3306    |
                    +----------------+

Email OTP / order notifications
              |
              v
        Gmail SMTP / App Password
```

---

# 1. Prerequisites

Before starting, make sure you have:

- AWS account
- GitHub account
- Basic Linux/SSH knowledge
- An email account that can be used for SMTP/app-password based mail sending
- The project source code
- AWS Region selected consistently for the resources

### Main AWS services used

| Service | Purpose |
|---|---|
| Amazon EC2 | Frontend and backend servers |
| Amazon RDS | Managed MySQL database |
| VPC | Network isolation |
| Security Groups | Control traffic between components |
| RDS Subnet Group | Places RDS in selected subnets |
| Application Load Balancer | Public entry point and traffic distribution |
| Target Group | Connects ALB to frontend EC2 |
| AWS Certificate Manager (ACM) | HTTPS/TLS certificate |
| Route 53 | Custom DNS/domain routing |
| Nginx | Frontend web server and API reverse proxy |
| Git/GitHub | Source-code management |
| SMTP | OTP and order email notifications |

---

# 2. Get the Application Code

The reference application contains separate frontend and backend components.

Reference repository:

https://github.com/CloudTechDevOps/aws-ecomerce-Application-Multiple-services

The deployment flow used here is:

1. Prepare the database.
2. Launch backend EC2.
3. Connect backend to RDS.
4. Start and verify the backend API.
5. Launch frontend EC2.
6. Install and configure Nginx.
7. Connect frontend `/api` requests to backend.
8. Create an ALB and Target Group.
9. Verify the website through the ALB.
10. Configure HTTPS using ACM.
11. Configure Route 53.
12. Test registration, OTP, cart and order flow.
13. Verify the order email.

---

# 3. Create the RDS MySQL Database

Open:

**AWS Console → RDS → Databases → Create database**

Recommended practice configuration:

- Engine: **MySQL**
- Template: Free Tier / appropriate practice option
- DB identifier: choose your own name
- Master username: choose your own username
- Password: create a strong password
- Database name: choose the name expected by your application
- Public access: preferably **No** when the backend EC2 can reach RDS privately
- VPC: use the same VPC as the backend
- Security group: create/use a dedicated RDS security group

### Important

Do not put a real database password inside GitHub.

Use environment variables or another secret-management approach.

---

# 4. Create the DB Subnet Group

Open:

**RDS → Subnet groups → Create DB subnet group**

Configure:

- Name: for example `db-subnet-group`
- VPC: project VPC
- Select the required Availability Zones
- Select private subnets where possible

The RDS instance should be placed in the intended subnets and protected by its security group.

### Screenshot

`docs/screenshots/DB-SUBNET-GROUP.png`

---

# 5. Configure RDS Security Group

Create a security group for RDS.

Inbound rule:

```text
Type: MySQL/Aurora
Port: 3306
Source: Backend EC2 Security Group
```

Avoid opening MySQL `3306` to the entire internet.

The desired communication is:

```text
Backend EC2 Security Group
          |
          | TCP 3306
          v
RDS Security Group
```

---

# 6. Verify the RDS Database

After RDS becomes available, copy the RDS endpoint.

Example format:

```text
your-db.xxxxxx.region.rds.amazonaws.com
```

From the backend EC2, test the connection:

```bash
mysql -h YOUR_RDS_ENDPOINT -u YOUR_DB_USER -p
```

Then import the application's SQL schema.

Example:

```bash
mysql -h YOUR_RDS_ENDPOINT -u YOUR_DB_USER -p < test.sql
```

Use your actual database password when prompted.

### Screenshot

`docs/screenshots/DATABASE.png`

---

# 7. Launch the Backend EC2 Server

Create an EC2 instance for the backend.

Typical setup:

- Amazon Linux
- Appropriate instance type for practice
- Project VPC
- Backend subnet
- Backend security group
- Key pair / approved access method

### Backend security-group requirements

Allow only the traffic required by the architecture.

For example:

```text
SSH 22       → your trusted source
Flask 5000   → frontend server / required internal source
```

Do not unnecessarily expose port `5000` publicly.

### Screenshot

`docs/screenshots/EC2_SERVERS.png`

---

# 8. Connect to Backend EC2

Connect through SSH or your preferred secure AWS access method.

Update the server:

```bash
sudo yum update -y
```

Install required packages:

```bash
sudo yum install -y git python3-pip mariadb105-server
```

Move to a working directory and clone the project:

```bash
sudo su -
git clone YOUR_GITHUB_REPO_URL aws-ecommerce-application
cd aws-ecommerce-application/backend
```

---

# 9. Configure Backend Python Environment

Create a virtual environment:

```bash
python3 -m venv venv
```

Activate it:

```bash
source venv/bin/activate
```

Upgrade pip:

```bash
pip install --upgrade pip
```

Install dependencies:

```bash
pip install -r requirements.txt
```

---

# 10. Configure Backend Environment Variables

Create the environment file:

```bash
vi .env
```

Use your own values:

```env
PORT=5000
FLASK_DEBUG=false

DB_HOST=YOUR_RDS_ENDPOINT
DB_USER=YOUR_DB_USER
DB_PASSWORD=YOUR_DB_PASSWORD
DB_NAME=YOUR_DB_NAME

MAIL_SERVER=smtp.gmail.com
MAIL_PORT=587
MAIL_USERNAME=YOUR_EMAIL
MAIL_PASSWORD=YOUR_EMAIL_APP_PASSWORD
```

### Security warning

Never commit `.env` to GitHub.

Add it to `.gitignore`:

```gitignore
.env
venv/
__pycache__/
```

---

# 11. Start and Test the Backend

You can first run the backend manually:

```bash
python3 app.py
```

Or use PM2/process management if it is part of your setup:

```bash
sudo dnf install -y nodejs npm
sudo npm install -g pm2

pm2 start app.py --name python-app --interpreter python3
pm2 status
pm2 logs python-app
pm2 save
```

Test the API locally:

```bash
curl http://127.0.0.1:5000/api
```

Expected result:

```json
{"message":"API is running successfully"}
```

### Screenshots

- `docs/screenshots/API_RUNNING_SUCCESSFULLY_MESSAGE.png`
- `docs/screenshots/BACKEND_SERVER_RUNNING.png`

---

# 12. Verify the Backend Database Connection

After the backend is running, perform an application-level test.

Check that:

- Backend can reach RDS
- Database tables are available
- Application can read/write required data
- Authentication-related operations work

### Screenshot

`docs/screenshots/CHECKING_DATABASE_AFTER_BUYING_ON_WEBSITE.png`

---

# 13. Launch the Frontend EC2 Server

Create a second EC2 instance for the frontend.

The frontend server will host the static website using Nginx.

Install packages:

```bash
sudo yum update -y
sudo yum install -y git nginx
```

Start Nginx:

```bash
sudo systemctl start nginx
sudo systemctl enable nginx
sudo systemctl status nginx
```

### Screenshot

`docs/screenshots/FRONTEND_SERVER_RUNNING.png`

---

# 14. Clone the Project on Frontend EC2

Clone the project:

```bash
sudo su -
git clone YOUR_GITHUB_REPO_URL aws-ecommerce-application
cd aws-ecommerce-application/frontend
```

---

# 15. Deploy Frontend Files to Nginx

Copy the frontend files to the Nginx web root.

Example:

```bash
sudo cp -r * /usr/share/nginx/html/
```

If the application contains a `main` directory:

```bash
sudo cp -r main/* /usr/share/nginx/html/
```

---

# 16. Configure Nginx Reverse Proxy

Create an Nginx configuration:

```bash
sudo vi /etc/nginx/conf.d/ecommerce.conf
```

Example:

```nginx
server {
    listen 80;
    server_name _;

    root /usr/share/nginx/html;
    index index.html;

    location = /api {
        proxy_pass http://BACKEND_PRIVATE_IP:5000/api;
        proxy_http_version 1.1;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    location /api/ {
        proxy_pass http://BACKEND_PRIVATE_IP:5000/api/;
        proxy_http_version 1.1;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    location / {
        try_files $uri $uri/ /index.html;
    }
}
```

Replace:

```text
BACKEND_PRIVATE_IP
```

with the private IP of the backend EC2.

Test the configuration:

```bash
sudo nginx -t
```

Restart Nginx:

```bash
sudo systemctl restart nginx
sudo systemctl status nginx
```

---

# 17. Test the Frontend Before ALB

Before introducing the load balancer, test that the frontend EC2 can serve the application.

Verify:

- Website loads
- Static files load
- API requests reach the backend
- Backend can communicate with RDS

### Screenshot

`docs/screenshots/CHECKING_WEBSITE_THROUGH_ALB.png`

> After ALB is configured, repeat the test using the ALB DNS name.

---

# 18. Create the Target Group

Open:

**EC2 → Load Balancing → Target Groups → Create target group**

Configuration:

```text
Target type: Instances
Protocol: HTTP
Port: 80
VPC: Project VPC
```

Register the **frontend EC2 instance**.

Health check:

```text
Protocol: HTTP
Port: traffic port
Path: /
```

Wait until the target becomes:

```text
Healthy
```

### Screenshot

`docs/screenshots/TARGET_GROUP.png`

---

# 19. Create the Application Load Balancer

Open:

**EC2 → Load Balancers → Create Load Balancer**

Select:

**Application Load Balancer**

Configure:

- Scheme: Internet-facing
- IP address type: IPv4
- Select the project VPC
- Select appropriate public subnets
- Attach the ALB security group

Create/listen using HTTP initially if required by your setup.

Forward traffic to:

```text
TG-1-frontend
```

---

# 20. Test the Website Through ALB

Copy the ALB DNS name.

Example:

```text
http://YOUR-ALB-DNS-NAME
```

Verify that the Flipkart-style e-commerce application loads.

Test:

- Homepage
- Product listing
- Login/signup
- Cart
- API calls
- Orders

### Screenshot

`docs/screenshots/CHECKING_WEBSITE_THROUGH_ALB.png`

---

# 21. Create an ACM Certificate

Open:

**AWS Certificate Manager (ACM)**

Request a public certificate for your domain.

Example:

```text
example.com
```

Complete domain validation.

If using DNS validation, create/allow the required Route 53 validation record.

---

# 22. Add HTTPS Listener to ALB

Open:

**EC2 → Load Balancers → ALB → Listeners and rules**

Create/update:

```text
Protocol: HTTPS
Port: 443
```

Select the ACM certificate.

Forward the request to:

```text
TG-1-frontend
```

The ALB should now show an HTTPS listener.

### Screenshot

`docs/screenshots/UPDATED_ALB_WITH_HTTPS.png`

---

# 23. Configure Route 53

Open:

**Route 53 → Hosted zones**

Create/use your hosted zone.

Create an `A` record:

```text
Record type: A
Alias: Yes
Alias target: Application Load Balancer
```

Point the domain to the ALB.

Then open:

```text
https://YOUR_DOMAIN
```

### Screenshot

`docs/screenshots/ROUTE53_WEBSITE.png`

---

# 24. Configure HTTPS Security

The intended public flow is:

```text
User
 |
 | HTTPS :443
 v
Application Load Balancer
 |
 | HTTP :80 inside the VPC
 v
Frontend EC2 / Nginx
 |
 | Internal API
 v
Backend EC2
 |
 | MySQL :3306
 v
RDS
```

Security groups should restrict communication to the required sources.

Recommended logical rules:

### ALB SG

```text
HTTPS 443 → Internet
HTTP 80   → Internet (only if required / for redirect)
```

### Frontend SG

```text
HTTP 80 → ALB Security Group
```

### Backend SG

```text
5000 → Frontend Security Group
```

### RDS SG

```text
3306 → Backend Security Group
```

---

# 25. Create a User Account

Open the website through the Route 53 domain.

Create a new account.

Verify that the registration flow works.

### Screenshot

`docs/screenshots/CREATED_AN_ACCOUNT.png`

---

# 26. Verify Email OTP

After registration/login, check the configured email inbox.

The application should send the OTP through the configured SMTP settings.

### Screenshot

`docs/screenshots/GETTING_OTP_ON_MAIL.png`

---

# 27. Add a Product to Cart

Open the product catalog.

Select a product and click **Add to Cart**.

Verify:

- Product appears in cart
- Quantity is correct
- Price is correct
- Cart API works
- Database state is updated as expected

### Screenshot

`docs/screenshots/PRODUCT_ON_CART.png`

---

# 28. Place an Order

Proceed through the checkout/order flow.

Verify:

- Order is created
- Order ID is generated
- Order status is updated
- Payment/order details are stored as expected
- Backend successfully writes the order to RDS

### Screenshot

`docs/screenshots/CHECKING_DATABASE_AFTER_BUYING_ON_WEBSITE.png`

---

# 29. Verify Order Confirmation Email

After the order is successfully placed, check the configured email inbox.

The application should send an order confirmation email containing the relevant order details.

### Screenshot

`docs/screenshots/ORDER_SUCCESSFUL_MAIL.png`

---

# 30. Final Application Verification

At this point verify the complete request flow:

```text
Route 53
   ↓
HTTPS
   ↓
ACM Certificate
   ↓
Application Load Balancer
   ↓
Target Group
   ↓
Frontend EC2
   ↓
Nginx
   ↓
Backend EC2
   ↓
Flask API
   ↓
Amazon RDS MySQL
   ↓
Application data
```

Also verify:

```text
User Registration
      ↓
Email OTP
      ↓
Login
      ↓
Product Selection
      ↓
Cart
      ↓
Order
      ↓
Database Update
      ↓
Order Confirmation Email
```

---

# 31. Screenshots / Evidence

The following screenshots document the major stages of the project.

| Screenshot | What it demonstrates |
|---|---|
| `DB-SUBNET-GROUP.png` | RDS subnet-group configuration |
| `DATABASE.png` | Database/RDS verification |
| `EC2_SERVERS.png` | EC2 server setup |
| `BACKEND_SERVER_RUNNING.png` | Backend server running |
| `API_RUNNING_SUCCESSFULLY_MESSAGE.png` | Backend API health check |
| `FRONTEND_SERVER_RUNNING.png` | Frontend/Nginx server |
| `TARGET_GROUP.png` | ALB target group |
| `CHECKING_WEBSITE_THROUGH_ALB.png` | Website accessed through ALB |
| `UPDATED_ALB_WITH_HTTPS.png` | ALB HTTPS listener + certificate |
| `ROUTE53_WEBSITE.png` | Website accessed using Route 53 domain |
| `CREATED_AN_ACCOUNT.png` | Account creation |
| `GETTING_OTP_ON_MAIL.png` | Email OTP |
| `PRODUCT_ON_CART.png` | Product added to cart |
| `CHECKING_DATABASE_AFTER_BUYING_ON_WEBSITE.png` | Database/order verification |
| `ORDER_SUCCESSFUL_MAIL.png` | Successful order email |

---

# 32. Troubleshooting Checklist

## Website not opening

Check:

```bash
sudo systemctl status nginx
sudo nginx -t
```

Also verify:

- ALB listener
- Target group health
- Frontend EC2 security group
- Nginx configuration
- Route 53 record

---

## Target is unhealthy

Check:

```bash
sudo systemctl status nginx
curl http://localhost
```

Then verify:

- Target group port is correct
- Health-check path exists
- Frontend SG allows traffic from ALB SG
- Nginx is listening on port 80

---

## Backend API not working

Check:

```bash
pm2 status
pm2 logs python-app
```

or:

```bash
python3 app.py
```

Then test:

```bash
curl http://127.0.0.1:5000/api
```

Check:

- Backend process
- Port 5000
- Backend SG
- Nginx reverse proxy
- RDS connectivity
- `.env` values

---

## Database connection failure

Check:

```bash
mysql -h YOUR_RDS_ENDPOINT -u YOUR_DB_USER -p
```

Verify:

- RDS is available
- Correct endpoint
- Correct username/password
- Port 3306
- RDS SG allows backend SG
- EC2 and RDS are in compatible VPC/network configuration

---

## OTP email not arriving

Verify:

- SMTP username
- SMTP app password
- SMTP port `587`
- Gmail SMTP configuration
- Application logs
- Recipient email address

Do not use your normal Gmail password when an app password is required.

---

# 33. Security Notes

This project is for learning and hands-on AWS practice.

Before using a similar architecture in production:

- Never commit `.env` files
- Never commit passwords
- Never expose RDS `3306` to `0.0.0.0/0`
- Restrict SSH access
- Use IAM roles instead of long-lived AWS access keys
- Use AWS Secrets Manager or another secret-management solution for production secrets
- Use HTTPS
- Keep EC2 packages updated
- Use least-privilege security groups
- Monitor logs and resource usage
- Review AWS costs and delete unused resources

---

# 34. Project Completion Checklist

- [x] RDS MySQL created
- [x] DB subnet group configured
- [x] Backend EC2 created
- [x] Backend code deployed
- [x] Backend dependencies installed
- [x] RDS schema imported
- [x] Backend connected to RDS
- [x] Backend API verified
- [x] Frontend EC2 created
- [x] Nginx installed
- [x] Frontend deployed
- [x] Nginx reverse proxy configured
- [x] Target Group created
- [x] ALB configured
- [x] Website verified through ALB
- [x] ACM certificate configured
- [x] HTTPS listener configured
- [x] Route 53 configured
- [x] Account creation tested
- [x] OTP email tested
- [x] Cart tested
- [x] Order tested
- [x] Database order verification completed
- [x] Order confirmation email verified

---

# 35. Credits / Reference

Application source/reference:

**CloudTechDevOps – AWS eCommerce Application Multiple Services**

https://github.com/CloudTechDevOps/aws-ecomerce-Application-Multiple-services

This repository is intended as my **step-by-step deployment documentation and learning record** for the AWS project.

---

## Author

**Venkata Subbarao**

AWS / Cloud Engineering Learner

GitHub: https://github.com/VENKATASUBBARAO13
