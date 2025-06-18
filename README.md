# AWS-VPC-Project
This project demonstrates a production-ready AWS VPC setup with public and private subnets, an Auto Scaling Group, a bastion host, and an Application Load Balancer (ALB) routing traffic to instances in private subnets.
# ✅ Summary
A fully functional VPC with isolated private instances
A bastion host for secure access
A load balancer exposing a demo app hosted in private subnets
A scalable group of EC2 instances behind the ALB


![image](https://github.com/user-attachments/assets/2546b3d7-e57d-41dd-8bcf-2e634f377834)



# 1. Create VPC
Auto-generate: aws-prod-example
Pv4 CIDR block:cidr
IPv6 CIDR block:No IPv^ CIDR Block
Tenancy :default
Number of Availability Zones (AZs): 2
Number of public subnets:2
Number of private subnets:2
NAT gateways ($): 1 per AZ
VPC endpoints: None
Create VPC


# 2. Launch Template for Auto Scaling
Name: aws-prod-example

Description: Proof of concept for app deployment

Instance Type: (e.g., t2.micro)

Key Pair: aws-login

Security Group: aws-prod-example

All traffic allowed (⚠️ for demo only — restrict in production)

Custom TCP: Port 8000

VPC: aws-prod-example

# 3. Auto Scaling Group
Name: aws-prod-example

Launch Template: previously created

Availability Zones: private-subnet-1 & private-subnet-2 in us-east-1a and us-east-1b

Desired Capacity: 2

Min Size: 1

Max Size: 4

Load Balancer: Not attached (yet)

Scaling Policies: None (manual scaling)

# 4. Bastion Host
Name: bastion-host

Key Pair: aws-login

Security Group: allows SSH (port 22)

Network Placement: Public subnet of same VPC

Usage:

Connect using:
```
ssh -i aws-login.pem ubuntu@<Bastion-Public-IP>
```

```
scp -i aws-login.pem SpringbootApplication.pem ubuntu@<Public-IP>:/home/ubuntu
```

# 5. Deploy Static Web Page on Private EC2
Connect via Bastion → Private IP

Example content:
<!DOCTYPE html>
<html>
<body>
  <h1>First AWS PROJECT to Demonstrate apps in private subnet</h1>
</body>
</html>
# Start simple Python web server:

```
python3 -m http.server 8000
```
# 6. Application Load Balancer (ALB)
Type: Internet-facing ALB

Name: aws-prod-example

VPC: aws-prod-example

AZ/Subnets: Public subnet 1 and 2

Security Group: aws-prod-example

Listener: HTTP on port 80

Target Group:

Name: aws-prod-example

Target Type: Instance

Port: 8000

Instances: Private EC2s (exclude bastion)

Health Check: HTTP on /

Routing: Listener routes to above target group


