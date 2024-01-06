## serverless service

Serverless service 是一種雲端服務，它允許您在不管理伺服器的情況下開發和部署應用程式。Serverless service 通常使用雲端供應商的自動化基礎設施來處理伺服器管理的所有細節，例如硬體、軟體、維護和更新。

* 簡化管理：您無需管理伺服器，因此可以專注於開發和部署應用程式。
* 失去可控性：您可能會失去對伺服器的某些控制權。

## relational database service (RDS)

Relational database service (RDS) 是一種雲端資料庫服務，它允許您在雲端中部署關係型資料庫。RDS 提供了多種關係型資料庫，例如 MySQL、PostgreSQL、Oracle 和 SQL Server。

> AWS 會自動幫你更新管理資料庫，計時付費

```
EBS (Elastic Block Store)
EFS (Elastic File System)
EIP (Elastic IP)
IAB
----- No SQL -----
BOTO3
S3 
route 53
Terraform
```

### 關閉 AWS 帳戶

![Alt text](image.png)

---

![Alt text](image-1.png)

![Alt text](image-2.png)

```bash
#!/bin/bash

########################################
##### USE THIS WITH AMAZON LINUX 2 #####
########################################

# get admin privileges
sudo su

# install httpd (Linux 2 version)
yum update -y
yum install -y httpd.x86_64
systemctl start httpd.service
systemctl enable httpd.service
echo "Hello World from $(hostname -f)" > /var/www/html/index.html
```
![Alt text](image-3.png)

## ALB

![Alt text](image-4.png)

![Alt text](image-5.png)

![Alt text](image-6.png)

![Alt text](image-7.png)

![Alt text](image-8.png)

![Alt text](image-9.png)

![Alt text](image-10.png)

![Alt text](image-11.png)

![Alt text](image-12.png)

![Alt text](image-13.png)

![Alt text](image-14.png)

![Alt text](image-15.png)

![Alt text](image-16.png)

![Alt text](image-17.png)

![Alt text](image-18.png)

![Alt text](image-19.png)

![Alt text](image-20.png)

![Alt text](image-21.png)

![Alt text](image-22.png)

