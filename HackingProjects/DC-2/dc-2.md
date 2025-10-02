# DC: 2 Walkthrough

## Preparation
1. Download DC-2.ova file ([DC-2.ova](https://download.vulnhub.com/dc/DC-2.zip))

1. Import the OVA file in the VirtualBox

1. Set the network adapter to Host-only Adapter
    * Attached to: **Host-only Adapter**
    ![Host-onlyAdapter](./img/dc-2-server_network.png)

1. Start the DC-2 virtual machine
    * Turn on the DC-2 virtual machine from the VirtualBox  
    ![StartDC-2](./img/dc-2-server_initial.png)  

1. Confirm the IP address of the DC-2 virtual machine from the attack virtual machine  
    * `sudo netdiscover -i enp0s3 -r 192.168.56.0/24`  
    ![netdiscover](./img/dc-2_server1.png)  
    ![DC-2-IP-Address](./img/dc-2-server2.png)  
        * 192.168.56.100: DHCP Server
        * **192.168.56.105**: DC-2 Server

1. Set the DC-2 IP address to the environment variance  
    * `export IP=192.168.56.105`  

## Reconnaissance
1. Do portscan using Nmap  
    * `sudo nmap -sC -sV -Pn -p- $IP -oN nmap_result.txt`  
    ![nmap](./img/dc-2-server3.png)
        * -sC: Scan with default script
        * -sV: Show software name and the version
        * -Pn: Do not confirm communication before port scan (We have already confirmed the DC-2 IP address.)
        * -p-: Scan all ports (from 0 to 65535 ports)
        * -oN: Output the scan results to the specified file

1. As we see the nmap result, we can attempt to access of 80(Http Service) and 7744(SSH Service) ports.  

## Initial Access
1. 