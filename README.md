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


