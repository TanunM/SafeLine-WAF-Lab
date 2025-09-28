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

