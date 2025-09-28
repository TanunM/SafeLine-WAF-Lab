# SafeLine WAF Home Lab

## Introduction
This document provides a comprehensive guide for setting up a web application security lab. The lab consists of a virtual environment featuring an attacker machine **Kali Linux** and a target server running a vulnerable web application **DVWA** on **Ubuntu Server**, protected by the **SafeLine Web Application Firewall (WAF)**. The primary objective is to simulate a basic web attack and analyze the WAF's capability to detect and mitigate the threat.

## Objective
The objective of this lab is to:
* Gain hands-on experience with WAF configuration, certificate management, and application onboarding.
* Configure the **SafeLine WAF** as a reverse proxy to protect the target application.
* Simulate a common web attack (**SQL Injection**) in a controlled environment.
* Monitor and analyze WAF logs to understand attack detection and blocking mechanisms.
* Develop practical defensive skills applicable to securing web infrastructure.

## Requirements and Tools
| Category | Requirements |
| :--- | :--- |
| **Operating Systems** | Kali Linux ISO, Ubuntu Server LTS ISO |
| **System Requirements** | RAM: $\ge 8$ GB, Disk Space: $\ge 50$ GB free |
| **Security Tools** | SafeLine WAF, DVWA (Damn Vulnerable Web App) |
| **Virtualization Software**| **VirtualBox** |

## Step 1: Setting Up Virtual Machines

### 1.1 Install Kali Linux
1.  Download the **Kali Linux ISO** from the [Kali Official Website](https://www.kali.org/get-kali).
2.  In VirtualBox, create a new VM. Allocate at least 2 GB RAM and 20 GB disk space.
3.  Install Kali Linux using the ISO file.

### 1.2 Install Ubuntu Server
1.  Download the latest **Ubuntu Server LTS ISO** from the [Ubuntu download site](https://ubuntu.com/download/server).
2.  Create a new VM in VirtualBox. Allocate at least 2 GB RAM and 20 GB disk space.
3.  Install Ubuntu Server.

### 1.3 Enable Bridged Networking
To have the VMs appear on the same network:
1. Open VirtualBox > select your VM  > Settings > Network.
2. Adapter 1: Choose Bridged Adapter.
4. Click OK.
5. Do the same step for both the machine.

## Step 2: Configuring Ubuntu Server

### 2.1 LAMP Installation and Configuring
1. Update the system and install packages, including Apache, PHP, and MySQL:
```bash
sudo apt-get update
sudo apt-get upgrade -y
sudo apt-get install -y net-tools
sudo apt-get install -y openssl
sudo apt install curl
sudo apt-get install -y net-tools git openssl apache2 php php-mysql mysql-server
```
2. Secure the MySQL Installation (Optional)
```bash
sudo mysql_secure_installation
```

### 2.2 Damn Vulnerable Web App (DVWA) Installation and Configuring
1. Clone DVWA
```bash
sudo apt-get install -y git
cd /var/www/html
sudo git clone https://github.com/digininja/DVWA.git
```
2. Set File Permissions
```bash
sudo chown -R www-data:www-data DVWA
sudo chmod -R 755 DVWA
```
3. DVWA has a config file at DVWA/config/config.inc.php. configure the following information
 ```
$DBMS = 'MySQL';
$db = 'dvwa';
$user = 'dvwa_user';
$pass = 'p@ssw0rd';
$host = 'localhost';
```
4. Create a new database and user in MySQL:

```bash
sudo mysql -u root -p
CREATE DATABASE dvwa;
CREATE USER 'dvwa_user'@'localhost' IDENTIFIED BY 'p@ssw0rd';
GRANT ALL ON dvwa.* TO 'dvwa_user'@'localhost';
FLUSH PRIVILEGES;
exit;
```
5. Initialize DVWA by Navigate to http://<Ubuntu IP>/DVWA/setup.php

### 2.3 optional changes
1. To change the DVWA Listening Port, edit the Apache Configuration file:
```bash
sudo nano /etc/apache2/ports.conf
```
2. change listining port
3. Update the default Virtual Host
```bash
sudo nano /etc/apache2/sites-available/000-default.conf
```
4. change <VirtualHost *:80> to your port
5.  Restart Apache
```bash
sudo systemctl restart apache2
```

### 2.4 Add Custom Values to the DVWA Database
1. Log in to MySQL and add sample table
```bash
sudo mysql -u root -p
USE dvwa;
CREATE TABLE test_users (
id INT NOT NULL AUTO_INCREMENT,
username VARCHAR(50) NOT NULL,
password VARCHAR(50) NOT NULL,
PRIMARY KEY (id)
);
INSERT INTO test_users (username, password) VALUES
('alice', 'alice123'),
('bob', 'bob123'),
('admin', 'admin123');
exit;
```
2. you can target this values in sql injection

## Step 3: DNS ResolutionSetup
1. Edit /etc/hosts on Both Ubuntu and Kali:
```bash
sudo nano /etc/hosts
```
3. Add <Ubuntu IP> dvwa.local
4. Now you can access the DVWA app at http://dvwa.local:8080/DVWA on Kali.

## Step 4: Creating a SSL Certificate
1. by using the following command create a ssl certificate
```bash
sudo mkdir /etc/ssl/dvwa
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
-keyout /etc/ssl/dvwa/dvwa.key \
-out /etc/ssl/dvwa/dvwa.crt \
```

## Step 5: Installing and Configuring SafeLine WAF

## 5.1 Install SafeLine WAF
1. On the Ubuntu Server, run the automatic deployment script
```bash
bash -c "$(curl -fsSLk [https://waf.chaitin.com/release/latest/manager.sh](https://waf.chaitin.com/release/latest/manager.sh))" -- --en
```
2. After the installation port, username and password will be provided in the terminal.
3. Log in to the management console by https://<Ubuntu IP>:9443

### 5.2 Onboard DVWA Application
1. Import Certificate by uploading the /etc/ssl/dvwa/dvwa.crt and /etc/ssl/dvwa/dvwa.key files to the WAF.
2. Configure Application
```
DNS Name: dvwa.local
WAF Listen Port: 443 (HTTPS).
Backend URL (Reverse Proxy): http://127.0.0.1:8080 (or http://192.168.20.10:8080).
Attach Certificate: Select the imported SSL certificate.
```

## Step 6: Simulating and Analyzing SQL Injection Attack
1. Open Kali Linux and browse to the DVWA site.
2. you will be redirected to https
3. Log In to DVWA
4. Set DVWA Security Level to low in the DVWA Security tab.
5. Go to SQL Injection Section and try typical SQL injection strings
```
admin' OR '1'='1
```
6. you will see SafeLine WAF detected and blocked the malicious injection attempt.

## Step 7: SafeLine WAF Advanced Configurations

### 7.1 HTTP Flood Defense
1. Enable HTTP Flood/DoS settings in SafeLine WAF.
2. Configure Thresholds and Penalty or Ban duration.
3. Test by sending multiple requests from Kali

### 7.2 Authentication Sign-In
1. Enable Auth Sign-In in the WAF policy for DVWA.
2. Configure username/password or integrate with an external auth provider.
3. Attempt to access DVWA from Kali.
4. The WAF should prompt for credentials before passing traffic to the server.

### 7.3 Custom Deny Rules
1. Identify the IP of your Kali VM
2. Add a Deny Rule in SafeLine WAF
```
Match Source IP: <Kali IP>
Action: Block or Deny.
```
3. Test from Kali: You will receive a blocked response.

## Troubleshooting
**Apache Default Page Redirect:** After changing the Apache listening port and restarting, accessing DVWA using http://<Ubuntu IP>:8080/DVWA sometimes redirects to the generic Apache default page. This usually happens if the DVWA application is not explicitly defined in the virtual host configuration.

To resolve it: 
1. Check Virtual Host: Open the configuration file: sudo nano /etc/apache2/sites-available/000-default.conf
2. Change Document Root: Ensure the file contains the following line, directing the server to the location of the DVWA folder: DocumentRoot /var/www/html
3. Restart Apache: sudo systemctl restart apache2

**WAF Not Intercepting:** If the WAF is not blocking attacks or HTTPS is not working.

Resolution:
Verify that Apache is running on the desired port and that the WAF's Backend URL is correctly pointing to this port

**Kali Connectivity**  If Kali cannot reach the WAF/Ubuntu machine.

Resolution:
Confirm that both VMs are on the same network (Bridged) and can ping each other's static IPs. Verify that the /etc/hosts file on Kali correctly maps dvwa.local to the Ubuntu IP.

## Key Lab Learnings
* Attacker Skills: Understanding of basic SQL injection structure.
* Defender Skills: WAF installation, SSL management, application onboarding, and policy configuration.
* Forensic Analysis: Log analysis to identify attack source and method.

