# VA-W2-Bluemoon-Vulnhub Writeup

## Lab Information (From Vulnhub)
- (Machine) Name: Bluemoon 2021
- Author: Kirthik-

Personal Note: This apparently only works with VirtualBox; couldn't find the Bluemoon IP on VMWare or other VM software (struggled with that for a bit)

## Tools Used (In KaliLinux)
- Nmap, Dirb, Gobuster, Hydra, Docker

## 1. Recon: Network Discovery
- The Attacker's Machine IP was identified with the command
```bash
ifconfig
```
- Attacker IP: 192.168.56.102

- The Target's IP was found with a network scan using the command
```bash
nmap -sn 192.168.56.0/24
```
- 

## 2. 
