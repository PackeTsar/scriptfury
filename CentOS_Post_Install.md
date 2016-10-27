# CentOS Minimal Server - Post-Install Setup ![logo]

Some setup processes to get CentOS 6 or 7 ready to use after initial install

CentOS Minimal Server download is available from: [http://isoredirect.centos.org/centos/7/isos/x86_64/CentOS-7-x86_64-Minimal-1511.iso][iso-download]

###   Network Setup   ###
-----------------------------------------
CentOS allows you to set the network up during the OS install, but sometimes you need to change it afterwards. CentOS 7 has a nice GUI you can use to do everything. CentOS 6 is a pain in the ass.

1. **CENTOS 7**: Use the GUI editor to make interface changes (easy button)

        sudo nmtui

2. **CENTOS 6**: Open the network configuration file for editing in VI (not easy button)

        sudo vi /etc/sysconfig/network-scripts/ifcfg-eth0

3. **CENTOS 6**: Modify and enter the below settings into the file (you have to hit "i" to enter edit/insert mode)

        IPADDR=192.168.1.100
        PREFIX=24
        GATEWAY=192.168.1.1
        DNS1=8.8.8.8
        DEFROUTE=yes
        IPV4_FAILURE_FATAL=yes
        IPV6INIT=yes
        IPV6_AUTOCONF=no
        IPV6_DEFAULTGW=2001:db8:1ce:1ce:babe::1
        IPV6ADDR=2001:db8:1ce:1ce:babe::100/64
        DNS2=2001:4860:4860::8888
        IPV6_DEFROUTE=yes
        IPV6_FAILURE_FATAL=yes
        NAME="System eth0"

4. **CENTOS 6**: Hit ESC to exit edit/insert mode, then type in ":w" and hit ENTER to write the file

5. **CENTOS 6**: Type in ":q" and hit ENTER to exit the editor

6. **CENTOS 6**: Open the other network config file to set the default gateway and hostname  (you have to hit "i" to enter edit/insert mode)

        sudo vi /etc/sysconfig/network

7. **CENTOS 6**: Enter gateway and hostname info as below

        NETWORKING=yes
        HOSTNAME=CENT6
        GATEWAY=192.168.1.1
        IPV6_DEFAULTGW=2001:db8:1ce:1ce:babe::1

8. **CENTOS 6**: Follow steps 4 and 5 above to save the file and exit the editor

9. **CENTOS 6**: Open the other resolv.conf file to set the DNS servers and search domains

        sudo vi /etc/resolv.conf

10. **CENTOS 6**: Enter DNS servers and search domain info as below

        search mydomain.com
        nameserver 8.8.8.8
        nameserver 2001:4860:4860::8888

11. **CENTOS 6**: Follow steps 4 and 5 above to save the file and exit the editor

12. Restart the networking stack to effect changes

        sudo /etc/init.d/network restart



###   Update the OS   ###
-----------------------------------------
It is always a good idea to update your OS with the latest patches when you build it

1. Check repos for available updates

        sudo yum update -y



###   Install Useful Packages   ###
-----------------------------------------
These are some packages I install on almost everything

1. Extra Packages for Enterprise Linux (EPEL) is an extensive database of useful and quality packages for RHEL-ish distro's. It automatically is seached by YUM once installed.

        sudo yum install epel-release -y

2. Open VM Tools is an open version of the VMWare Tools app. Useful if on a hypervisor

        sudo yum install open-vm-tools -y

3. A GIT client is a necessity if using a GIT repo

        sudo yum install git -y



###   Connect to SCP Share   ###
-----------------------------------------
We can use SSHFS to connect to another Linux box file system

1. Install the SSHFS package

        sudo yum install sshfs -y

2. Mount the local /mnt/ directory to a remote Linux machine

        sshfs root@192.168.100.100:/root /mnt



[logo]: http://www.packetsar.com/wp-content/uploads/script-fury-small.png
[iso-download]: http://isoredirect.centos.org/centos/7/isos/x86_64/CentOS-7-x86_64-Minimal-1511.iso