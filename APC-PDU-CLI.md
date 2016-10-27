# APC PDU/UPS Network - Management Card CLI Setup ![logo]

A config script for the management card which makes configuration a breeze compared to the GUI


1. Set hostname, location, new admin user, and reset password for default user

        system -n PDU-HOSTNAME-HERE
        system -l "Cabinet#XXXX Right Side (Viewed from HOT ISLE)"
        user -n someuser -pe Administrator -pw somepassword
        user -n apc -cp apc -pw somepassword
        
2. Set DNS servers, NTP servers, system contact

        dns -p 8.8.8.8 -s 8.8.4.4
        ntp -p 173.71.69.90 -s 208.94.243.142
        system -c "System Contact Name, Phone Number, EMail Address"

3. Disable the other default users and enable the new user, disable HTTP, enable HTTPS, and set date format and timezone
        
        system -s enable
        user -n device -e disable
        user -n readonly -e disable
        user -n someuser -e enable
        web -h disable -s enable
        ftp -S enable
        date -z -08:00
        date -f yyyy-mm-dd

4. Set IP address, gateway, and search domain
        
        tcpip -i 10.32.2.56
        tcpip -s 255.255.255.0
        tcpip -g 10.32.2.1
        tcpip -d ibddomain.com

5. Set longer CLI prompt, disable TELNET, and enable SSH

        prompt -s long
        console -t disable
        console -s enable

6. Reboot the Management Card to effect changes (this will not take down power on the PDU ports, just reboot the management card)
    
        reboot



[logo]: http://www.packetsar.com/wp-content/uploads/script-fury-small.png