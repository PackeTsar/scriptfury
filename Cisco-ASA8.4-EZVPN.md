# Cisco ASA8.4+ - EZVPN Setup ![logo]

Cisco EZVPN lets you set up dynamic VPNs to a remote device with its own IP space

        Corporate Network                 Internet Cloud                         Remote Network
          172.16.0.0/20<------nat------->{(172.25.0.0/16)}<--------nat---------->172.16.3.48/29

###   ASA Server Side Configuration   ###
-----------------------------------------

        ------Remote Network Object-------
        !
        object network EZVPNPOOL
         subnet 172.16.3.48 255.255.255.240


        ------No NAT Statement for remote network-------
        !
        nat (inside,any) source static any any destination static EZVPNPOOL EZVPNPOOL no-proxy-arp


        ------Outside route for remote network (sometimes needed on the ASA)-------
        !
        route outside 172.16.3.48 255.255.255.240 172.25.26.1


        ------Phase 1 Settings and interface mapping-------
        !
        crypto ipsec ikev1 transform-set EZVPNSET esp-aes esp-sha-hmac
        !
        crypto dynamic-map VPNDYN-MAP 5 set ikev1 transform-set EZVPNSET
        !
        crypto map VPNMAP 60 ipsec-isakmp dynamic VPNDYN-MAP
        !
        crypto map VPNMAP interface outside
        !
        crypto ikev1 enable outside
        crypto ikev1 policy 1
         authentication pre-share
         encryption des
         hash md5
         group 2
         lifetime 86400
        

        ------Group Policy Settings-------
        group-policy EZVPNGP internal
        group-policy EZVPNGP attributes
         vpn-tunnel-protocol ikev1
         password-storage enable
         split-tunnel-policy tunnelspecified
         split-tunnel-network-list value EZVPN
         nem enable
        

        ------Tunnel Group Settings-------
        tunnel-group EZVPNTUNNEL type remote-access
        tunnel-group EZVPNTUNNEL general-attributes
         default-group-policy EZVPNGP
        tunnel-group EZVPNTUNNEL ipsec-attributes
         ikev1 pre-shared-key somepresharedkeyhere



###   IOS EZVPN Client Config   ###
-----------------------------------------
Config on an IOS router to be a EZVPN client

        ------Private Network DHCP Settings-------
        ip dhcp excluded-address 172.16.3.49
        !
        ip dhcp pool PRIVATE_DHCP
           import all
           network 172.16.3.48 255.255.255.248
           default-router 172.16.3.49


        ------EZVPN Client Configuration-------
        crypto ipsec client ezvpn EZVPN1
         connect auto
         group EZVPNTUNNEL key somepresharedkeyhere
         mode network-extension
         peer 172.25.26.250
         username localuseronasa password userpassword
         xauth userid mode local


        ------Interface Settings-------
        interface FastEthernet0/0
         ip address dhcp
         duplex auto
         speed auto
         crypto ipsec client ezvpn EZVPN1
        !
        interface FastEthernet0/1
         ip address 172.16.3.49 255.255.255.248
         duplex auto
         speed auto
         crypto ipsec client ezvpn EZVPN1 inside


        ------Static Route Setting-------
        ip route 0.0.0.0 0.0.0.0 dhcp



[logo]: http://www.packetsar.com/wp-content/uploads/script-fury-small.png