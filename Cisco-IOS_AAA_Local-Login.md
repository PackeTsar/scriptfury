# Cisco IOS - AAA Local Login ![logo]

A quick script to configure local console and SSH login with no enable secret on Cisco IOS

    aaa new-model
    aaa authorization exec default local if-authenticated
    aaa authentication login CONSOLE local
    aaa authorization console
    !
    banner login         
        ************************************************************************
        * Any or all uses of this system and all files on this system may      *
        * be intercepted, monitored, recorded, copied, audited, inspected,     *
        * and disclosed to authorized site and law enforcement personnel,      *
        * as well as authorized officials of other agencies, both domestic     *
        * and foreign.  By using this system, the user consents to such        *
        * interception, monitoring, recording, copying, auditing, inspection,  *
        * and disclosure at the discretion of authorized site personnel.       *
        *                                                                      *
        * Unauthorized or improper use of this system may result in            *
        * administrative disciplinary action and civil and criminal penalties. *
        * By continuing to use this system you indicate your awareness of and  *
        * consent to these terms and conditions of use.   LOG OFF IMMEDIATELY  *
        * if you do not agree to the conditions stated in this warning.        *
        ************************************************************************
            
    !
    line vty 0 15
    login authentication default
    transport input ssh
    line console 0
    login authentication CONSOLE
    end



[logo]: http://www.packetsar.com/wp-content/uploads/script-fury-small.png