# Raven: 1 Walkthrough

## Preparation
1. Download Raven.ova file ([Raven.ova](https://download.vulnhub.com/raven/Raven.ova))

1. Import the OVA file in the VirtualBox

1. Set the network adapter to Host-only Adapter
    * Attached to: **Host-only Adapter**
    ![Host-onlyAdapter](./img/evilbox-server_network.png)    

1. Start the Raven1 virtual machine
    * Turn on the Raven1 virtual machine from the VirtualBox  
    ![StartPwnLab](./img/evilbox-server_initial.png)  

1. Confirm the IP address of the Raven1 virtual machine from the attack virtual machine  
    * `sudo netdiscover -i enp0s3 -r 192.168.56.0/24`  
    ![netdiscover](./img/raven1_server1.png)  
    ![PwnLab-IP-Address](./img/evilbox-server2.png)  
        * 192.168.56.100: DHCP Server
        * **192.168.56.110**: Raven1 Server

1. Set the Raven1 IP address to the environment variance  
    * `export IP=192.168.56.110`  