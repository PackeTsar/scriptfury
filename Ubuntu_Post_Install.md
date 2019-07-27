# Ubuntu Server - Post-Install Setup ![logo]

Some setup processes to get Ubuntu 14 or 16 ready to use after initial install

Ubuntu Server download is available from: [https://www.ubuntu.com/download/server][iso-download]


###   NMTUI Network Setup (Recommended)   ###
-----------------------------------------
NMTUI (Network Manager Text User Interface) is an easy to use GUI which makes it easy to set up your networking. You must have internet access to install it.

1. Install Network Manager

        sudo apt install network-manager

2. Enable the Network Managers service for startup and start the service

        sudo systemctl enable NetworkManager
        sudo systemctl start NetworkManager

3. Use `sudo vi /etc/netplan/50-cloud- init.yaml` to make the file look like:

```
# This file is generated from information provided by
# the datasource.  Changes to it will not persist across an instance.
# To disable cloud-init's network configuration capabilities, write a file
# /etc/cloud/cloud.cfg.d/99-disable-network-config.cfg with the following:
# network: {config: disabled}
network:
    version: 2
    renderer: NetworkManager
```

4. Restart the server with `sudo shutdown -r now`

5. Once back up, run the network manager with `nmtui`

6. Edit your network settings and make sure to deactivate/activate the interface if you are on the console. If not on the console, reboot the server to have settings take effect

7. Use `ip addr` to check that your settings took effect


###   Manual Network Setup   ###
-----------------------------------------
If you didn't setup the network adapters during the initial install (like CentOS does), we have to login (using non-root user) to the KVM and set up the network as the first thing

1. Open the 'interfaces' file for editing

        sudo nano /etc/network/interfaces

2. Modify and enter the information below based on the adapters listed already in the file and the IP info you need to assign

        auto eth0
        iface eth0 inet static
                address 192.168.1.100
                netmask 255.255.255.0
                gateway 192.168.1.1
                dns-nameservers 8.8.8.8
        iface eth0 inet6 static
                address 2001:db8:1ce:1ce:babe::100/64
                gateway 2001:db8:1ce:1ce:babe::1
                dns-nameservers 2001:4860:4860::8888 2001:4860:4860::8844

3. Hit CTRL-O and then ENTER to save the file

4. Hit CTRL-X to exit the Nano editor

5. **UBUNTU 14**: Restart the networking stack to effect changes

        sudo ifdown eth0
        sudo ifup eth0

6. **UBUNTU 16**: Restart the networking stack to effect changes

        sudo /etc/init.d/networking restart

7. Check that the IP settings took

        ip addr

8. Ping something to check connectivity

        ping 8.8.8.8



###   Enable Root User   ###
-----------------------------------------
Ubuntu has the root user disabled by default and requires you to log in with a user created during the install process. Sometimes we need to enable the root account

1. Set the password for the root account

        sudo passwd root
2. Unlock the root account

        sudo passwd -u root

3. **UBUNTU 14**: Enable SSH login as root user (optional)

        sudo sed -i --follow-symlinks 's/PermitRootLogin without-password/PermitRootLogin yes/g' /etc/ssh/sshd_config

        sudo service ssh restart

3. **UBUNTU 16**: Enable SSH login as root user (optional)

        sudo sed -i --follow-symlinks 's/PermitRootLogin prohibit-password/PermitRootLogin yes/g' /etc/ssh/sshd_config

        sudo service ssh restart



###   Update the OS   ###
-----------------------------------------
You may have selected some level of automatic updates during the install, but it is always a good idea to update your OS with the latest patches when you build it

1. Check repos for available updates

        sudo apt update -y

2. Install available updates

        sudo apt upgrade -y



###   Install Useful Packages   ###
-----------------------------------------
These are some packages I install on almost everything

1. **UBUNTU 16**: Python 2.X is something you will likely need

        sudo apt install python-minimal -y

2. Open VM Tools is an open version of the VMWare Tools app. Useful if on a hypervisor

        sudo apt install open-vm-tools -y

3. A GIT client is a necessity if using a GIT repo

        sudo apt install git -y



###   Connect to SCP Share   ###
-----------------------------------------
We can use SSHFS to connect to another Linux box file system

1. Install the SSHFS package

        sudo apt install sshfs -y

2. Mount the local /mnt/ directory to a remote Linux machine

        sshfs root@192.168.100.100:/root /mnt



[logo]: http://www.packetsar.com/wp-content/uploads/script-fury-small.png
[iso-download]: https://www.ubuntu.com/download/server
