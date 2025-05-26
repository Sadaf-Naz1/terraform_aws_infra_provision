# Terraform AWS Infrastructure Setup

This project uses **Terraform** to provision a complete infrastructure on **AWS**, including:

- Custom **VPC**
- Public and private **subnets**
- **Internet Gateway**
- **NAT Gateway**
- **Route tables**
- **Security groups**
- **EC2 instances**
- **Bastion host**
- **Elastic Load Balancer (ELB)**
- **Auto Scaling Group**

---

## 📌 Pre-requisites

1. **AWS IAM Credentials**
   - Access Key ID
   - Secret Access Key

2. **Terraform**
   - [Install Terraform](https://www.terraform.io/downloads.html)

---

## 📦 AWS Resources Provisioned

- VPC with CIDR block `10.0.0.0/16`
- Public and private subnets
- Internet Gateway
- NAT Gateway (with Elastic IP)
- Route Tables (public & private)
- Security Groups (bastion, web, ELB)
- Bastion EC2 instance (public subnet)
- Application EC2 instances (private subnet)
- Elastic Load Balancer
- Auto Scaling Group

---

## 🗂️ Terraform File Structure

### 1. `provider.tf`
Configures AWS provider and credentials.

### 2. `variables.tf`
Declares input variables used across modules.

### 3. `modules/networking/`
Reusable module that defines:
- VPC
- Subnets
- IGW & NAT
- Route Tables

### 4. `main.tf`
Main configuration to set up:
- Network infrastructure
- Security Groups
- EC2 instances
- Key pairs

### 5. `production.tf`
Root module that references variables and modules.

### 6. `outputs.tf`
Defines output values like:
- VPC ID
- Subnet IDs
- ELB DNS
- Bastion IP

### 7. `instances.tf`
Creates:
- Bastion EC2 instance
- Private EC2 instances (app servers)

### 8. `elb.tf`
Defines an Application Load Balancer.

### 9. `elb-security-groups.tf`
Security groups for:
- Port 80 (HTTP)
- All outbound traffic

### 10. `elb-instances.tf`
Creates EC2 instances registered with the ELB.

### 11. `install_httpd.sh`
User data script to install Apache:

```bash
#! /bin/bash
sudo yum update
sudo yum install -y httpd
sudo chkconfig httpd on
sudo service httpd start
echo "<h1>Deployed via Terraform with ELB</h1>" | sudo tee /var/www/html/index.html
```
### 12. `autoscaling.tf`
Uses the terraform-aws-modules/autoscaling/aws to configure:
- Launch template
- Auto Scaling Group

## 🚀 Deploying the Infrastructure

Run the following Terraform commands to deploy the infrastructure:

```bash
# Initialize Terraform
terraform init

# Preview infrastructure changes
terraform plan

# Apply the infrastructure
terraform apply
```
## ✅ Testing and Validation

### 1. SSH into Bastion Host

Use the command below to connect to the bastion host located in the public subnet:

```bash
ssh -i ec2Key.pem ec2-user@<bastion_public_ip>
```
### 2. SSH into Private EC2 via Bastion

Once you're connected to the bastion, SSH into the private EC2 instance:

```bash
ssh -i ec2Key.pem ec2-user@<private_instance_ip>
```
> ⚠️ **Important:** Ensure your private key file has the correct permissions before connecting:

```bash
chmod 400 ec2Key.pem
```