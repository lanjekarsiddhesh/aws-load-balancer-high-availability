# AWS Load Balancer High Availability Project

This project builds a highly available AWS web architecture using multiple EC2 instances behind an Application Load Balancer (ALB). It ensures continuous website access even if a server fails by automatically distributing traffic across instances, significantly improving system availability, reliability, and performance.

## What is AWS?
Amazon Web Services (AWS) is a comprehensive cloud computing platform provided by Amazon that delivers on-demand IT infrastructure and services via the internet. As one of the world’s leading cloud platforms, it enables organizations to deploy scalable computing, storage, databases, and analytics without the overhead of maintaining physical data centers.

## AWS Services Used
- **IAM (Identity and Access Management):** Used to securely manage access and permissions for AWS resources.
- **Amazon EC2 (Elastic Compute Cloud):** Provides scalable, virtual computing instances (servers) in the cloud.
- **Amazon EBS (Elastic Block Store):** Delivers high-performance, persistent block storage volumes designed for use with EC2 instances.
- **Amazon ALB (Application Load Balancer):** Manages incoming traffic to ensure the application stays continuously running.
- **Security Group:** Acts as a virtual firewall to control inbound and outbound traffic for the EC2 instances.
- **Target Group:** A logical routing pool that tells the load balancer exactly which EC2 instances, containers, or IP addresses to direct incoming traffic to.

## Core Concepts Explained

### 1. AWS IAM
This service helps you securely control access to AWS resources. It allows you to manage users, roles, and permissions to strictly define who or what can access your AWS environment. IAM is a **global service**, meaning it is not region-specific and is managed centrally.

### 2. Amazon EC2
Amazon EC2 provides virtual machines (Instances) in the cloud. Instead of purchasing physical hardware, you can launch these virtual servers in minutes and easily scale them up or down depending on your traffic demand.

### 3. Amazon EBS
EBS is a cloud-based storage service that provides durable, high-performance block storage volumes. It functions exactly like a virtual hard drive attached to a physical server, ensuring data persists independently of the lifespan of the EC2 instance.

### 4. Amazon ALB
An ALB is a traffic controller that automatically distributes incoming web traffic across multiple targets (like EC2 instances) to balance the workload.

### 5. Security Group
A Security Group acts as a stateful, host-level firewall for EC2 instances, allowing you to set explicit rules to control both inbound and outbound network traffic.

### 6. Target Group
A Target Group acts as a routing destination for the load balancer. Instead of sending traffic directly to individual servers, the load balancer sends traffic to the target group, which then intelligently distributes the requests across the registered instances.

## Architecture Diagram
<img width="1221" height="994" alt="image" src="https://github.com/user-attachments/assets/ef8bd118-33ac-4f8f-84ad-b72648c36a89" />

## Steps Performed
1. **IAM Setup:** Created an IAM user with `AdministratorAccess` for project deployment, and created an IAM instance role with the `AmazonSSMManagedInstanceCore` policy attached.
2. **EC2 Provisioning:** Launched 2 EC2 instances using a startup script (user data) to automatically install and configure the Nginx web server.
3. **Security Group Configuration:** Adjusted security group rules to allow specific inbound traffic (SSH and HTTP) while permitting all outbound traffic.
4. **Role Assignment:** Attached the created IAM role to the EC2 instances to grant them permissions to interact securely with AWS systems.
5. **Service Verification:** Accessed the EC2 instances and verified that the Nginx service was running successfully.
6. **Storage Expansion:** Created two 10GB EBS volumes in the same Availability Zones (AZs) as the EC2 instances and attached them.
7. **Storage Management:** Formatted and mounted the EBS volumes to a local directory path.
8. **Nginx Reconfiguration:** Modified the default Nginx configuration file to point the web root directory to the newly mounted EBS volume location.
9. **Setup Target Group:** Created a target group and assigned the EC2 instances to it.
10. **Setup ALB:** Created and configured the ALB, linking it directly to the created target group.
11. **Test ALB Web Hosting:** Copied the ALB DNS URL to test the website. Refreshed the page multiple times to verify traffic distribution across different EC2 web servers.
12. **Failover Testing:** Stopped one EC2 instance and verified that traffic diverted seamlessly to the remaining healthy server. The website remained accessible with zero downtime.
13. **Scale Out:** Created a 3rd EC2 instance and added it to the existing target group.
14. **Final Load Balancing Test:** Restarted the stopped instance so all 3 web servers were active. Verified that the ALB successfully balanced traffic among all three active servers.

## IAM Policies Utilized
- `AdministratorAccess` (Used for initial user environment creation)
- `AmazonSSMManagedInstanceCore` (Used to allow secure systems management and EC2 access)

## Linux Commands Reference
---
```bash
# Install Nginx server
sudo dnf update -y
sudo dnf install nginx -y

# Verify, enable, and start the Nginx web server
sudo systemctl status nginx
sudo systemctl enable nginx
sudo systemctl start nginx

# Create a deployment directory structure
sudo mkdir -p /data/website/html

# Storage administration: View storage devices, format, and mount the EBS volume
lsblk
sudo mkfs.ext4 /dev/xvdf
sudo mount /dev/xvdf /data
df -h

# Modify Nginx configuration (Change root directory)
sudo vi /etc/nginx/nginx.conf
sudo systemctl restart nginx

# Deploy web content
cd /data/website/html
vi index.html

# Safely unmount volume during migration/cleanup
sudo umount /data
```
## Hosted website output
- Server 1
<img width="1920" height="1080" alt="ALB-web-1-output" src="https://github.com/user-attachments/assets/cba1081d-6a10-4f18-90f0-2a7e2fce516d" />
- Server 2
<img width="1920" height="1080" alt="ALB-Web-2-output" src="https://github.com/user-attachments/assets/5e594083-7c28-4475-b037-3c3e5817ab76" />
- Load Balancer Overview
<img width="1920" height="1080" alt="ALB-3_resource" src="https://github.com/user-attachments/assets/a66d3241-0816-4f14-bbbe-88c0e182bdf4" />

## Conclusion
This project shows how to stop a website from crashing if a server breaks down. By using an AWS Load Balancer to share the work among EC2 instances, the website stays fast and online. Even if one computer completely stops working, users won't notice a thing because their traffic is automatically sent to a working one. This keeps the website up and running smoothly at all times.
- **Fault Tolerance:** The website stays online because AWS constantly checks on the health of the servers. If one server breaks down, AWS instantly spots it and stops sending users there, keeping the website working smoothly.
- **Optimized Performance:** Website traffic is shared equally among all servers, which keeps any single server from getting overloaded and slowing down the website.
