# About Attack Virtual Machine

Although Kali is more popular than ParrotOS, I select ParrotOS. The reason is that, as Kali is frequently updated, the system or user interface may be changed significantly.  

## Make ParrotOS Virtual Machine
1. Access to the official ParrotOS site and download ParrotOS Security Edition iso image  
[ParrotOS-SecurityEdition](https://parrotsec.org/download/)
1. Creat Virtual Machine from VirtualBox
    * Input the name (Note: Recommend to include the OS version)
    ![Create1](../img/VirtualBox_Create1.png)
    * Set the hardware  
    Main memory: 4096 MB  
    Processors: 8
    ![Create2](../img/VirtualBox_Create2.png)
    * Set the hard disk  
    Hard disk size: 40 GB  
    ![Create3](../img/VirtualBox_Create3.png)

## Set Configuration of ParrotOS Virtual Machine
1. Enable copy and paste
    * Shared Clipboard: Bidirectional
    * Drag'n'Drop: Bidirectional
    ![Set1](../img/VirtualBox_Setting1.png)
1. Increase video memory
    * Video memory: 128 MB
    ![Set2](../img/VirtualBox_Setting2.png)
1. Enable USB 3.0
    * Select USB 3.0 (xHCI) Controller
    ![Set3](../img/VirtualBox_Setting3.png)  
1. Enable Network Adapter
    * Attached to: **NAT** in the Adapter 1
    ![Set4](../img/VirtualBox_Setting4.png)
    * Adapter 2, 3, 4 are not to set  

## Install ParrotOS to the Empty Virtual Machine
1. Start ParrotOS machine from the VirtualBox
1. Install ParrotOS
1. Shutdown the system
1. Remove the ISO file from the storage
    * As there is a iso file in the red part, you remove the file.
    ![Install1](../img/VirtualBox_ParrotInstall1.png)

## Preparing for attacking
1. Enable John the Ripper  
    Add a PATH in the shell profile  
    * Input the following command in the terminal  
        `vi ~/.bashrc`  
    * Add the following sentence in the last row  
        `export PATH=$PATH:/usr/sbin`
    * Reload the shell configuration  
        `source ~/.bashrc`
1. Extract rockyou.txt.gz file from the terminal  
    `gunzip /usr/share/wordlists/rockyou.txt.gz`
1. Install FTP command  
    `sudo apt update`  
    `sudo apt install ftp`

## Change Network Adapter Setting
1. Network adapter setting from NAT to **Host-only Adapter**  
    ![Setting5](../img/VirtualBox_Setting5.png)  
    Since the host-only adapter can communicate within the virtual network, no damage occurs even if the machine attempts to attack a public service machines.