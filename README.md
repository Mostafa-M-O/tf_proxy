# Terraform Project with Nginx and Flask

## Project Overview

This project sets up a VPC in AWS with both public and private subnets, deploying EC2 instances as Nginx reverse proxies and Flask web application backends. It also configures NAT Gateway, Internet Gateway, and two load balancers (public and internal).
## project Structure 
<img width="1228" height="658" alt="image" src="https://github.com/user-attachments/assets/6cbd38dd-3d37-404b-a1d8-46cacca58fc7" />
)

## Architecture

* **VPC**: Custom CIDR blocks for public and private subnets.
* **Public Subnets**: 10.0.0.0/24 and 10.0.2.0/24 containing Nginx reverse proxy EC2 instances.
* **Private Subnets**: 10.0.1.0/24 and 10.0.3.0/24 containing Flask application backends.
* **NAT Gateway**: For private subnets to access the internet.
* **Internet Gateway**: For public subnet internet access.
* **Load Balancers**:

  * Public ALB: Routes traffic to Nginx proxies.
  * Internal ALB: Routes traffic from proxies to private backend servers.

## Terraform Setup

### Workspace

* Create a new workspace `dev`.

  ```bash
  terraform workspace new dev
  ```

### Backend

* Configure remote backend for state file storage.

### Modules

* Each module is implemented with:

  * `main.tf`
  * `variables.tf`
  * `outputs.tf`
* Custom modules are used for VPC, EC2 instances, and load balancers.

### Provisioners

* **Local-exec**: Prints all IPs to `all-ips.txt` in the format:

  ```
  public-ip1 98.94.77.49
  public-ip2 52.87.152.158
  private-ip-1 10.0.1.27
  private-ip-2 10.0.3.54
  ```




### Proxy Configure

```
server {
    listen 80;
    server_name _;
    resolver 10.0.0.2 valid=30s;
    set $backend_server "http://${var.internal_alb_dns}";
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;
    location / {
        proxy_pass $backend_server;
    }
}
```

### Accessing the App

* Access public ALB -> Nginx proxies -> Internal ALB -> Flask backends.

## Quick Start

1. Clone the repository:

```bash
git clone <repository-url>
cd <project-folder>
```

2. Initialize Terraform and select workspace:

```bash
terraform init
terraform plan
terraform apply
```


3. When done, destroy resources to avoid charges:

```bash
terraform destroy
```

## Notes

* IP addresses and DNS names of EC2 instances and ALBs are saved in `all-ips.txt`.
