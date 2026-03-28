# VA-W2-Bluemoon-Vulnhub Writeup

## Lab Information (From Vulnhub)
- (Machine) Name: Bluemoon 2021
- Author: Kirthik-
- OS: Linux/Debian

**Personal Note:** This apparently only works with VirtualBox; couldn't find the Bluemoon IP on VMWare or other VM software (struggled with that for a bit)

## Tools Used (In KaliLinux)
- Nmap, Dirb, Gobuster, Hydra, Docker

## 1. Recon Stage: Network Discovery
- The Attacker's Machine IP was identified with the command:
```bash
ifconfig
```
- Attacker IP: 192.168.56.102

- The Target's IP was found with a network scan using the command:
```bash
nmap -sn 192.168.56.0/24
```
- Target IP: 192.168.56.101 (Bluemoon IP)

## 2. Scanning Stage: Port Scanning
- A detailed nmap scan was performed with the command:
```bash 
nmap -sC -sV -p- 192.168.56.101
```
- The scan discovered three open ports: 21,22 and 80.
- Port 21 | Service: ftp | Version: vsftpd 3.0.3
- Port 22 | Service: ssh | Version: OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0) 
- Port 80 | Service: http | Version: Apache httpd 2.4.38 ((Debian))

## 3. Gaining Access: Web Enumeration + FTP Enumeration + File Analysis
- The target IP was entered into the browser 192.168.56.101 where a page with the text **"-- Welcome -- Are You Ready To Play With Me .....!"** and a image with the text **"The Game Begins"**.

To find out any hidden files/directories gobuster was used with the command:
```bash
gobuster dir -u http://<IP> -w /usr/share/wordlists/dirb/common.txt
```
- The result revealed a /hidden_text directory with a 200 OK Status
- Adding the directory to the target IP in the browser revealed a page with text displaying "Maintanance! Sorry For Delay. We Will Recover Soon" along with blue clickable text "Thank you..."
- Clicking on the "Thank you..." led to another page with a QR code that after analysis provided the logins:
> USER: userftp 
> PASSWORD: ftpp@assword

- FTP access was made with:
```bash
ftp 192.168.56.101
```
- Where the login credentials were used to successfully enter the system
- ```ls``` was used to check the file system that revealed two files:
  - information.txt
  - p_lists.txt



















