# Raspberry Pi: Raspbian - Post-Install Setup ![logo]

Some setup processes to get Raspbian Lite ready to use after initial install

Raspbian download is available from: [https://www.raspberrypi.org/downloads/raspbian/][iso-download]



###   Network Setup   ###
-----------------------------------------
Getting a Raspberry Pi connected to a KVM so you can configure the network is a pain.

An easy way to avoid this is to have the Pi enable SSH when it boots up and make sure a DHCP server will serve it an IP address. You can have the Pi enable SSH on boot up by creating an empty file called 'ssh' on the root folder when you install the Raspbian image. 

1. Create the emtpy file (named "ssh") in the root directory of the SD card to enable SSH on boot up after the image install.


2. Install the SD card, connect the Pi to a network with DHCP enabled and power it on.


3. After waiting a few minutes for boot up, SSH to the Pi's DHCP address
  - Log in to SSH with:
    - Username: pi
    - Password: raspberry


4. Install Network Manager with:
```
apt install network-manager
systemctl enable NetworkManager
systemctl start NetworkManager
systemctl stop dhcpcd.service
systemctl disable dhcpcd.service
```

 > Make sure to stop the DHCPCD service or else the network stack will start resetting every few seconds


5. Reboot the Pi to have the new network settings take effect
```
sudo shutdown -r now
```

6. Run `nmtui` once Pi is back up and adjust network settings



###   Enable Root User   ###
-----------------------------------------
Raspbian has the root user disabled by default and requires you to log in with the default user ("pi") after install. Sometimes we need to enable the root account

1. Set the root password
`sudo passwd root`

2. Enable root login with SSH
`sudo sed -i --follow-symlinks 's/#PermitRootLogin prohibit-password/PermitRootLogin yes/g' /etc/ssh/sshd_config`

3. Restart the SSH daemon
`sudo systemctl restart ssh`

4. Logout and log back into SSH with the root user
`exit`

5. Copy the terminal settings from the default user for pretty colors
```
mv .bashrc .bashrcbackup
cp /home/pi/.bashrc ~/.bashrc
```



###   Update the OS   ###
-----------------------------------------
It is always a good idea to update your OS with the latest patches when you build it

Update your Repo list
`apt update`

Install all updates for the OS
`apt upgrade -y`



###   Install Useful Packages   ###
-----------------------------------------
These are some packages I install on almost everything

1. A useful package that will install python2 and some development tools.

`sudo apt install -y python-pip`

2. A GIT client is a necessity if using a GIT repo

`sudo apt install -y git`




[logo]: http://www.packetsar.com/wp-content/uploads/script-fury-small.png
[iso-download]: https://www.raspberrypi.org/downloads/raspbian/
