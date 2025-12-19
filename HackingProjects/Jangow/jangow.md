# Jangow: 1.0.1 Walkthrough

## Preparation
1. Download Jangow.1.0.1.ova file ([Jangow.1.0.1.ova](https://download.vulnhub.com/jangow/jangow-01-1.0.1.ova))

1. Add the Jangow.1.0.1.ova file in the VirtualBox

1. Set the network adapter to Host-only Adapter
    * Attached to: **Host-only Adapter**
    ![Host-onlyAdapter](./img/jangow_server_network.png)    

1. Start the Jangow.1.0.1 virtual machine
    * Turn on the Jangow.1.0.1 virtual machine from the VirtualBox  
    ![Jangow.1.0.1](./img/jangow_server_initial.png)  

1. Confirm the IP address of the Jangow.1.0.1 virtual machine from the attack virtual machine  
    * `sudo netdiscover -i enp0s3 -r 192.168.56.0/24`  
    ![netdiscover](./img/jangow_server1.png)  
    ![Jangow.1.0.1-IP-Address](./img/jangow_server2.png)  
        * 192.168.56.100: DHCP Server
        * **192.168.56.118**: Jangow.1.0.1 Server  

1. Set the Jangow.1.0.1 IP address to the environment variance  
    * `export IP=192.168.56.118`  

