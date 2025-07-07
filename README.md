# End-to-End Application Deployment on AWS (EC2, RDS, Load Balancer)

This project provides a complete Terraform configuration to deploy a highly available web application on AWS. The architecture includes a VPC with public and private subnets, EC2 instances in an Auto Scaling Group behind an Application Load Balancer (ALB), and a highly available RDS MySQL database in private subnets. Security groups are tightly configured to allow only necessary traffic. All critical outputs such as ALB DNS, EC2 instance IPs, and the RDS endpoint are provided.

---

## Architecture Overview

```
                    +------------------------------+
                    |      AWS Cloud               |
                    +------------------------------+
                            |
                            |
                    +----------------------+
                    |   VPC (10.0.0.0/16)  |
                    +----------------------+
                     /           \           
         +----------------+   +----------------+
         | Public SubnetA |   | Public SubnetB |
         | 10.0.1.0/24    |   | 10.0.2.0/24    |
         +----------------+   +----------------+
                  |                    |
         +----------------+   +----------------+
         |  ALB           |   |  ALB           |
         +----------------+   +----------------+
                  |                    |
         +-------------------------------+
         |   Auto Scaling Group (EC2)     |
         |    (Public Subnets)            |
         +-------------------------------+
                     |
         +-------------------------------+
         |   Private SubnetA & B          |
         |   (10.0.11.0/24 & 10.0.12.0/24)|
         +-------------------------------+
                     |
         +-------------------------------+
         |         RDS MySQL             |
         +-------------------------------+
```

---

## Features

- **VPC** with public and private subnets across multiple AZs for high availability.
- **Internet Gateway** and proper route tables for public subnets.
- **Application Load Balancer (ALB)** in public subnets for distributing HTTP traffic.
- **Auto Scaling Group** of EC2 instances (Amazon Linux 2) behind the ALB.
- **RDS MySQL** deployed in private subnets with Multi-AZ for high availability.
- **Security Groups**:
  - ALB: Exposes HTTP (80) to the internet.
  - EC2: Allows HTTP (80) from ALB only.
  - RDS: Allows MySQL (3306) only from EC2 instances.
- **User Data** bootstraps web servers with a basic web page and installs Apache.
- **Terraform Outputs**: ALB DNS name, EC2 instance IPs, and RDS endpoint.

---

## Requirements

- Terraform >= 1.0
- AWS CLI configured with appropriate credentials
- AWS account with permissions to create the described resources

---

## Getting Started

### 1. Clone the Repository

```bash
git clone https://github.com/<your-org>/<your-repo>.git
cd <your-repo>
```

### 2. Initialize Terraform

```bash
terraform init
```

### 3. Review or Edit Variables (Optional)

Variables are hardcoded in `main.tf` for demonstration. For production, use separate `variables.tf` and `terraform.tfvars` files.

### 4. Apply the Configuration

```bash
terraform apply
```
- Review the plan and type `yes` to continue.

---

## Outputs

After deployment, Terraform provides the following outputs:

- **alb_dns_name**: DNS address of the Application Load Balancer.
- **web_instance_public_ips**: Private IP addresses of EC2 instances.
- **db_endpoint**: Endpoint address of the RDS MySQL database.

---

## Security

- **Do not use the default RDS password in production!** Change it before applying.
- Security groups are set to least privilege.
- EC2 inbound only allows HTTP from the ALB.
- RDS only accepts connections from EC2 security group.

---

## Clean Up

To destroy all resources created by this project:

```bash
terraform destroy
```
Type `yes` to confirm.

---

## File Structure

```
.
├── main.tf         # Main Terraform configuration (all resources)
├── README.md       # Project documentation
```

> For production, consider splitting resources into separate files/modules and using remote state.

---

## Notes

- The provided AMI is for us-east-1. Replace it with the latest Amazon Linux 2 AMI for your region.
- Change RDS credentials before applying!
- The example user data creates a simple web page. Replace with your application deployment logic as needed.

---

## License

MIT License.

---

## Author

[shyama-devops](https://github.com/shyama-devops)
