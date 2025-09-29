# DC: 1 Walkthrough

## Preparation
1. Download DC-1.ova file ([DC-1.ova](https://download.vulnhub.com/dc/DC-1.zip))

1. Import the OVA file in the VirtualBox

1. Set the network adapter to Host-only Adapter
    * Attached to: **Host-only Adapter**
    ![Host-onlyAdapter](./img/dc-1-server_network.png)

1. Start the Potato virtual machine
    * Turn on the potato virtual machine from the VirtualBox  
    ![StartPotato](./img/dc-1-server_initial.png)  

1. Confirm the IP address of the Potato virtual machine from the attack virtual machine  
    * `sudo netdiscover -i enp0s3 -r 192.168.56.0/24`  
    ![netdiscover](./img/dc-1_server1.png)  
    ![PotatoIP-Address](./img/dc-1-server2.png)  
        * 192.168.56.100: DHCP Server
        * **192.168.56.104**: DC-1 Server

1. Set the DC-1 IP address to the environment variance  
    * `export IP=192.168.56.104`  

## Reconnaissance
1. Do portscan using Nmap  
    * `sudo nmap -sC -sV -Pn -p- $IP -oN nmap_result.txt`  
    ![nmap](./img/dc-1-server3.png)
        * -sC: Scan with default script
        * -sV: Show software name and the version
        * -Pn: Do not confirm communication before port scan (We have already confirmed the Potato IP address.)
        * -p-: Scan all ports (from 0 to 65535 ports)
        * -oN: Output the scan results to the specified file

1. As we see the nmap result, we can attempt to access of 22, 80, and 111 ports.  

## Initial Access
1. Access the HTTP service using ParrotOS browser    
    * Enter `http://192.168.56.104:80`  
    ![HTTP-Access](./img/dc-1-server4.png)  

1. Confirm exisiting the "robots.txt file" 
    * Enter `http://192.168.56.104/robots.txt`  
    ![Robot-txt](./img/dc-1-server5.png)  
        - "Disallow": Deny crawler access  

1. Access "/MAINTAINERS.txt
    * It implies the service uses Drupal version 7  
    ![MAINTAINERS-txt](./img/dc-1-server6.png)  

1. Use droopescan (CMS vulnerability scanner)
    * Set the target URL to the environment variance  
    `export URL="http://$IP:80/`  
    * Scan using droopescan  
    ![droopescan](./img/dc-1-server7.png)
        - 5 plugins
        - 2 theme
        - Possible versions (7.22 - 7.26)
        - Possible interesting URL (/user/login)

1. Investigate Exploits for Drupal
    * Access to the [Exploit Database](https://www.exploit-db.com/)  
    * Search exploitations for the following keywords  
    `drupal 7 remote`  
    ![Remote-Exploit](./img/dc-1-server8.png)  
        - Memorize the "**Drupalgeddon**"

## Execution
1. Start Metasploit
    * Try to attack with "Drupalgeddon" vulnerability


## Privilege Escalation
1. 

## Credential Access for root user
1. 