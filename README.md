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

### 2.1 Initial Setup and LAMP Installation
Update the system and install packages, including Apache, PHP, and MySQL:
```bash
sudo apt-get update
sudo apt-get upgrade -y
sudo apt-get install -y net-tools
sudo apt-get install -y openssl
sudo apt install curl
sudo apt-get install -y net-tools git openssl apache2 php php-mysql mysql-server
```
