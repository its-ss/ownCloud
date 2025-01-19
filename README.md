# Secure File Share & Sync Solution Using ownCloud and AWS

## 📌 Overview
This project implements **ownCloud** on **AWS** to provide a secure file-sharing and syncing solution. It helps organizations replace third-party file-sharing services (e.g., Dropbox) with a self-hosted alternative to ensure **data security, privacy, and compliance**.

## 📌 Architecture

### 📌 Components:
- **Custom VPC** with two subnets: Public & Private
- **MySQL Database** on a **private subnet**
- **ownCloud Application** on a **public subnet**
- **Apache HTTP Server** configured to host ownCloud
- **NAT Instance** for internet access to private subnet
- **Security Groups** configured to allow only necessary traffic

### 📌 Architecture Diagram
*(Insert architecture diagram here if available.)*

---

## 🚀 Implementation Steps

### 🏗️ Step 1: VPC and Subnet Creation
1. Create a **VPC** with **CIDR block** `10.0.0.0/16`
2. Create a **Public Subnet** (`10.0.1.0/24`) and enable **Auto-Assign Public IP**
3. Create a **Private Subnet** (`10.0.2.0/24`)

### 🌍 Step 2: Internet Gateway and Routing
1. **Create & Attach an Internet Gateway** to the VPC
2. **Public Route Table** → Associate with the public subnet
3. **NAT Instance** → Deploy in the public subnet for private subnet internet access
4. **Private Route Table** → Associate with the private subnet

---

## ☁️ Step 3: EC2 Instance Setup
#### 🔹 Application Server (ownCloud)
- **Ubuntu 18.04** instance in **public subnet**
- Open security group ports: `80, 443, 22`
- Assign a key pair for **SSH access**

#### 🔹 Database Server (MySQL)
- **Ubuntu 18.04** instance in **private subnet**
- Open security group ports: `22, 3306`
- Use the same **key pair** for SSH access

---

## 🔧 Step 4: Install & Configure Apache and PHP
1. **Connect to Application Server** via SSH:
   ```bash
   ssh -i your-key.pem ubuntu@<PUBLIC_IP>
   ```
2. **Install Apache Web Server**:
   ```bash
   sudo apt-get update
   sudo apt-get install apache2 -y
   ```
3. **Validate Installation**: Open a browser and visit:
   ```
   http://<PUBLIC_IP>
   ```
4. **Install PHP**:
   ```bash
   sudo apt install php libapache2-mod-php php-mysql -y
   ```
5. **Make `index.php` the Default Load Page**:
   ```bash
   sudo nano /etc/apache2/mods-enabled/dir.conf
   ```
   Modify the line:
   ```
   DirectoryIndex index.php index.html index.cgi index.pl index.xhtml index.htm
   ```
6. **Restart Apache**:
   ```bash
   sudo systemctl restart apache2
   ```

---

## 📦 Step 5: Install & Configure MySQL
1. **Connect to the Database Server**:
   ```bash
   ssh -i your-key.pem ubuntu@<PRIVATE_IP>
   ```
2. **Install MySQL**:
   ```bash
   sudo apt update
   sudo apt install mysql-server -y
   ```
3. **Secure MySQL**:
   ```bash
   sudo mysql_secure_installation
   ```
4. **Create Database & User**:
   ```sql
   CREATE DATABASE owncloud_db;
   CREATE USER 'owncloud_user'@'%' IDENTIFIED BY 'StrongPassword!';
   GRANT ALL PRIVILEGES ON owncloud_db.* TO 'owncloud_user'@'%';
   FLUSH PRIVILEGES;
   EXIT;
   ```

---

## 💬 Step 6: Install & Configure ownCloud
1. **Download ownCloud**:
   ```bash
   curl https://attic.owncloud.org/download/repositories/10.2/Ubuntu_18.04/Release.key | sudo apt-key add -
   echo 'deb http://attic.owncloud.org/download/repositories/10.2/Ubuntu_18.04/ /' | sudo tee /etc/apt/sources.list.d/owncloud.list
   sudo apt update
   sudo apt install php-bz2 php-curl php-gd php-imagick php-intl php-mbstring php-xml php-zip owncloud-files -y
   ```
2. **Configure Apache for ownCloud**:
   ```bash
   sudo nano /etc/apache2/sites-enabled/000-default.conf
   ```
   Change:
   ```
   DocumentRoot /var/www/owncloud
   ```
3. **Restart Apache**:
   ```bash
   sudo systemctl reload apache2
   ```
4. **Access ownCloud via Web Browser**:
   ```
   http://<PUBLIC_IP>/owncloud
   ```
   Follow the setup wizard and enter:
   - **Database Name**: `owncloud_db`
   - **Database User**: `owncloud_user`
   - **Password**: `StrongPassword!`
   - **Database Host**: `<PRIVATE_IP>`

---

## 🔍 Troubleshooting & Fixes
1. **SSH Connection Issues**
   - If you cannot SSH into your instance, ensure:
     - The **correct key pair** is used.
     - **Security group** allows **port 22**.
2. **ownCloud Not Accessible**
   - Restart Apache:
     ```bash
     sudo systemctl restart apache2
     ```
   - Check Apache logs:
     ```bash
     sudo tail -f /var/log/apache2/error.log
     ```
3. **Database Connection Errors**
   - Verify MySQL is running:
     ```bash
     sudo systemctl status mysql
     ```
   - Check MySQL logs:
     ```bash
     sudo tail -f /var/log/mysql/error.log
     ```

---

## 🧹 Cleanup
Since AWS follows a **pay-per-use** model, **delete all resources in reverse order** after completing the project.

---

## 🔗 Resources
- [AWS VPC Documentation](https://docs.aws.amazon.com/vpc/)
- [ownCloud Official Site](https://owncloud.com/)
- [MySQL Official Site](https://www.mysql.com/)
- [How to Install Apache & PHP](https://www.digitalocean.com/community/tutorials/how-to-install-linux-apache-mysql-php-lamp-stack-ubuntu-18-04)

---

## 👤 Author
[Suyash Shukla](https://github.com/yourgithub)

---

### 📜 License
This project is licensed under the **MIT License**.

---
