# SeedBox-Lacie
 SeedBox-Lacie is a project that aims to help torrent users to build their own home-made seed-box.
 In this documentation we will build a DIY home server that will seed 24/7 using a raspberrypi and a vpn to enhance 
 our online privacy and security
---
## Requirements
    Hardware:
        - RaspberryPi Board
        - RaspberryPi Case (Optional)
        - A 5.1V 3.0A DC output Charger with USB-C output connector
        - A microSD Card with at least 4GB sotrage space
        - A computer with internet connection and a microSD card slot
        - A router/modem to connect the RaspberryPi to
        - A HDD to store the content that will be shared
        - LAN cable (optional)
 
    Software:
        - RaspberryPi Imager
        - Qbittorrent-nox
        - A VPN that provides 
        - WireGuard
---
## Table of Contents:
1. Setting up the RaspberryPi
   - Download and deploy of the OS
   - Starting and connecting the RaspberryPi to the internet
   - Connect to the board with through SSH
   - Configure the RaspberryPi and update the OS
2. Installing a torrent client
   - Installing qBittorrent
   - Running qBittorrent as a Service
   - Accessing the Web Interface for qBittorrent
   - Reserve IP address or set a static one for RaspberryPi
3. External Storage
   - External HDDs
   - Unlock if needed
   - Mount the HDD to a location
   - Changing Permissions
4. Setting up FTP server
   - Install FTP Server
   - Config FTP Server
   - Add FTP User
   - Install a FTP client
5. Port Forwarding
   - Forward torrent client's port
---
## 1. Setting-up the RaspberryPi
### Download and deploy of the OS:
 - Download the RaspberryPi imager from [Downlaod Page](https://www.raspberrypi.com/software/)
 - Insert the MicroSD Card and install RaspberryPi Lite OS
   Make sure to enable SSH protocol. Here, Wi-Fi connectivity can also be set so the pi will automatically connect 
to the internet
 - Put the MicroSD into the RaspberryPi and plug the charger, it will power on by itself
### Starting and connecting the RaspberryPi to the internet:
 - Access the router to see if the Pi has connected to the internet and get its IP address. If not, connect the Pi 
with a LAN cable.
### Connect to the board with through SSH:
 - Connect to the Pi with a terminal via ```ssh pi@[raspberrypi-ip-address]```
### Configure the RaspberryPi and update the OS
 - Any configuration can be made easily with ```sudo raspi-config```
 - Update the OS ```sudo apt update``` & ```sudo apt upgrade```
## 2. Installing a torrent client
### Installing qBittorrent
 - ```sudo apt install qbittorrent-nox```
### Running qBittorrent as a Service
 - ```sudo useradd -r -m qbittorrent``` creates a user for qbittorrent
 - ```sudo usermod -a -G qbittorrent pi``` will add pi user to qbittorrent group
 - ```sudo nano /etc/systemd/system/qbittorrent.service``` creates a service that will autostart qbittorrent
 - Within this file, we want to enter the following lines of text:


      [Unit]
      Description=qBittorrent
      After=network.target
    
      [Service]
      Type=forking
      User=qbittorrent
      Group=qbittorrent
      UMask=002
      ExecStart=/usr/bin/qbittorrent-nox -d --webui-port=8080
      Restart=on-failure
    
      [Install]
      WantedBy=multi-user.target

 - ```sudo systemctl start qbittorrent``` starts our newly created qBittorrent service
 - ```sudo systemctl status qbittorrent``` checks service status
 - ```sudo systemctl enable qbittorrent``` make it start when our Raspberry Pi boots
### Accessing the Web Interface for qBittorrent
 - Access link ```http://[RaspberryPi IP ADDRESS]:8080```
 - When accessing your qBittorrent web interface, you will need to log in. The default credentials are admin:adminadmin
### Reserve IP address or set a static one for RaspberryPi
 - Reserve an Ip address for the pi from the router's interface or set up a static ip. 
How to: https://www.freecodecamp.org/news/setting-a-static-ip-in-ubuntu-linux-ip-address-tutorial/
## 3. External Storage
### External HDDs
 - External HDDs are providing large storages at a lower price than SSDs. Make sure the storage device is formatted 
in a file system that linux operates. Recommended Ext4 file system
 - ```lsblk -l``` check for the HDD's disk and partitions
### Unlock if needed
 - check for partitions ```lsblk -l``` This will display a list of all block devices in a tabular format
 - Use cryptsetup to unlock an encrypted drive ```sudo apt-get update``` && ```sudo apt-get install cryptsetup``` && 
```sudo cryptsetup luksOpen /dev/sda2 lacie```, 
### Mount the HDD to a location
```sudo mount /dev/mapper/lacie /home/qbittorrent/Lacie/```
 - ```lsblk -l``` to check if the partition is mounted
### Changing Permissions
 - ```sudo chown -R qbittorrent:qbittorrent /home/qbittorrent/Lacie/``` to change the ownership of the mounted drive so the torrent
client will have required permissions
## 4. Setting up FTP server
### Install FTP Server
 - Ensure your Raspberry Pi is up-to-date with the latest software packages. Open a terminal window and run the 
following command: ```sudo apt update && sudo apt upgrade```
 - Install FTP Server: Install the vsftpd FTP server package using the following command: ```sudo apt install vsftpd```
### Config FTP Server
 - Edit Configuration File: Open the vsftpd configuration file with a text editor with root privileges using nano or 
vim: ```sudo nano /etc/vsftpd.conf```. Locate the anonymous_enable directive and change its value to no to disable 
anonymous login: ```anonymous_enable=no```
 - Uncomment the lines or add them at the end of the file
 > local_enable=YES  
   write_enable=YES  
   local_umask=022  
   chroot_local_user=YES  
   user_sub_token=$USER  
   local_root=/home/ftpusers/$USER  
 > 
In this example, we assume the home directory for your FTP user will be /home/ftpusers/username. Replace "username" with the desired username.
### Add FTP User
 - Create the FTP user and directory: Now, you need to create the FTP user and the associated home directory.
> sudo useradd -m -d /home/ftpusers/username -s /bin/bash username  
   sudo passwd username  
   sudo mkdir -p /home/ftpusers/username  
   sudo chown -R username:username /home/ftpusers/username  
   Replace "username" with the actual username you want to create.  
>
 - After making changes, restart the vsftpd service for the new configuration to take effect 
```sudo service vsftpd restart```. Now, you should be able to connect to your FTP server using the specified username 
and the configured home directory. Ensure that the directory has the necessary permissions for the FTP user to 
read and write files. Adjust the configuration and commands based on your specific requirements and system setup.
 - You can use the id command to check if a user exists. For example: ```id username```
### Config FTP Server
 - Make sure that the ```/etc/vsftpd.chroot_list``` file exists. You can create an empty file if it doesn't exist:
```touch /etc/vsftpd.chroot_list```. Ensure that the vsftpd.chroot_list file has the correct permissions. Set the 
appropriate: ```chmod 600 /etc/vsftpd.chroot_list``` If you want to chroot specific users, list those usernames in 
this file. ```nano /etc/vsftpd.chroot_list```
 - Restart vsftpd: ```sudo service vsftpd restart```
### Install a FTP client
 - Install FileZilla to copy files ```sudo apt update``` & ```sudo apt install filezilla```
 - Connect to RaspberryPi with its IP address, FTP username and password and FTP server port (default 21)
## 5. Port Forwarding
### Forward torrent client's port
 - To improve the efficiency of peer-to-peer (P2P) file sharing forward the torrent client's port. The port can be 
found in the settings section of the torrent client and must be forwarded from the router 
https://www.hellotech.com/guide/for/how-to-port-forward