# Cisco ASA8.4+ - Anyconnect Setup ![logo]

A few different scripts for setting up Anyconnect with some different authentication options

###   Anyconnect Basic Setup   ###
-----------------------------------------
Common configuration for any Anyconnect setup

    object network SSLVPNCLIENTS
     subnet 10.136.15.0 255.255.255.0
    !
    object-group network RFC1918
     network-object 10.0.0.0 255.0.0.0
     network-object 172.16.0.0 255.240.0.0
     network-object 192.168.0.0 255.255.0.0
    !
    !
    access-list SSLVPN_SPLIT_TUNNEL extended permit ip object-group NET_RFC1918 object SSLVPNCLIENTS
    !
    ip local pool SSLVPNPOOL 10.136.15.10-10.136.15.254 mask 255.255.255.0
    !
    nat (inside,outside) source static RFC1918 RFC1918 destination static SSLVPNCLIENTS SSLVPNCLIENTS no-proxy-arp
    !
    webvpn
     enable outside
     anyconnect image disk0:/anyconnect-win-3.1.00495-k9.pkg 1
     anyconnect enable
     tunnel-group-list enable



###   Local Authentication   ###
-----------------------------------------
Configuration to authenticate Anyconnect against local ASA users

    username someuser password somepassword privilege 15
    !
    group-policy SSLVPN_GRP_PLCY attributes
     dns-server value 4.2.2.2
     vpn-tunnel-protocol ssl-client
     split-tunnel-policy tunnelspecified
     split-tunnel-network-list value SSLVPN_SPLIT_TUNNEL	
    !
    tunnel-group SSLVPN_TUN_GRP general-attributes
     address-pool SSLVPNPOOL
     default-group-policy SSLVPN_GRP_PLCY



###   Active Directory Authentication (Any AD User)   ###
-----------------------------------------
Configuration to authenticate Anyconnect against Active Directory and allow any user with an AD account

    aaa-server AD_LDAP protocol ldap
    aaa-server AD_LDAP (inside) host 10.124.131.221
     ldap-base-dn DC=myaddomain,DC=com
     ldap-scope subtree
     ldap-naming-attribute sAMAccountName
     ldap-login-password serviceaccountpassword$$$$
     ldap-login-dn CN=vpnserviceaccount,OU=Users,DC=myaddomain,DC=com
     server-type microsoft
    !
    group-policy SSLVPN_GRP_PLCY internal
    group-policy SSLVPN_GRP_PLCY attributes
     dns-server value 4.2.2.2
     vpn-tunnel-protocol ssl-client
     split-tunnel-policy tunnelspecified
     split-tunnel-network-list value SSLVPN_SPLIT_TUNNEL
    !
    tunnel-group SSLVPN_TUN_GRP type remote-access
    tunnel-group SSLVPN_TUN_GRP general-attributes
     address-pool SSLVPNPOOL
     authentication-server-group AD_LDAP
     default-group-policy SSLVPN_GRP_PLCY
    ??tunnel-group SSLVPN_TUN_GRP webvpn-attributes
     ??group-alias ADAUTH



###   Active Directory Authentication (Specific AD Group Allowed)   ###
-----------------------------------------
Configuration to authenticate Anyconnect against Active Directory and allow only users in a specific security group

**NOTE:** *In order to authenticate against a particular security group in active directory, you have to default the tunnel-group to a group-policy that has 0 vpn-simultaneous-logins so that a user who has good credentials but isn't in the security group will fail to authenticate. Then create a ldap attribute map that maps the user who is a member of the group to a group-policy with > 0 allowed vpn-simultaneous-logins.*

    ldap attribute-map SSHVPN_LDAP_MAP
      map-name  memberOf Group-Policy
      map-value memberOf CN=SSLVPN_ALLOWED,CN=Users,DC=myaddomain,DC=com SSLVPN_GRP_PLCY
    !
    aaa-server AD_LDAP protocol ldap
    aaa-server AD_LDAP (inside) host 10.124.131.221
     ldap-base-dn DC=myaddomain,DC=com
     ldap-scope subtree
     ldap-naming-attribute sAMAccountName
     ldap-login-password serviceaccountpassword$$$$
     ldap-login-dn CN=vpnserviceaccount,OU=Users,DC=myaddomain,DC=com
     server-type microsoft
     ldap-attribute-map SSHVPN_LDAP_MAP
    !
    group-policy NOACCESS internal
    group-policy NOACCESS attributes
     vpn-simultaneous-logins 0
    !
    group-policy SSLVPN_GRP_PLCY internal
    group-policy SSLVPN_GRP_PLCY attributes
     dns-server value 4.2.2.2
     vpn-tunnel-protocol ssl-client
     split-tunnel-policy tunnelspecified
     split-tunnel-network-list value SSLVPN_SPLIT_TUNNEL
    !
    tunnel-group SSLVPN_TUN_GRP type remote-access
    tunnel-group SSLVPN_TUN_GRP general-attributes
     address-pool SSLVPNPOOL
     authentication-server-group AD_LDAP
     default-group-policy NOACCESS
    ??tunnel-group SSLVPN_TUN_GRP webvpn-attributes
     ??group-alias ADAUTH



[logo]: http://www.packetsar.com/wp-content/uploads/script-fury-small.png