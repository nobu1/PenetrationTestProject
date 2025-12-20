# Empire: LupinOne Walkthrough

## Preparation
1. Download Empire-Lupin-One.zip file ([Empire-Lupin-One.ova](https://download.vulnhub.com/empire/01-Empire-Lupin-One.zip))

1. Extract the zip file  

1. Import the Empire-Lupin-One.ova file in the VirtualBox

1. Set the network adapter to Host-only Adapter
    * Attached to: **Host-only Adapter**
    ![Host-onlyAdapter](./img/lupin_server_network.png)    

1. Start the Empire-Lupin-One virtual machine
    * Turn on the Empire-Lupin-One virtual machine from the VirtualBox  
    ![Deathnote](./img/lupin_server_initial.png)  

1. Confirm the IP address of the Empire-Lupin-One virtual machine from the attack virtual machine  
    * `sudo netdiscover -i enp0s3 -r 192.168.56.0/24`  
    ![netdiscover](./img/lupin_server1.png)  
    ![Empire-Lupin-One-IP-Address](./img/lupin_server2.png)  
        * 192.168.56.100: DHCP Server
        * **192.168.56.115**: Empire-Lupin-One Server  

1. Set the Empire-Lupin-One IP address to the environment variance  
    * `export IP=192.168.56.115`  
