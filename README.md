# Mssante_EPISEN
Bind9,postfix,dovecot,Squirrelmail
 mettre en place un serveur mail sur un système linux
 domaine local 
 dns 
 MTA
 MTU
 MUA
## Environment setup
Ubuntu 16.04 is required to install the package "squirrelmail" using an Apt-based PackageManagement tool.Furthermore, Squirrelmail required php5 which is more easy to install in the verision 16.04 of ubuntu.

### Virtualbox Network configuration

Gestinonnaire de réseau hôte > Créer 

in the tab "Interface" :

select "configurer la carte manuellement"
        
        @IPv4                           :   192.168.0.100
        Masque réseau @IPv4             :   255.255.255.0
        
in the tab "Serveur DHCP" :

        @IPv4 du serveur                :   192.168.0.100
        Masque réseau @IPv4             :   255.255.255.0
        Limite inférieur des adresses   :   192.168.0.1
        Limite supérieur des adresses   :   192.168.0.10
        
 Click on "Apply" to save the current configuration 


### Ubuntu 16.04 LTS

Recover the display capabilities of the host on the virtual machine :

        sudo apt install build-essential dkms linux-headers-$(uname -r)
        add Virtual Guest Additions # via Vbox windows 

Update the system :

        apt-get update
        apt-get upgrade


## Service installation

### Serveur DNS
*bind9

        apt-get intall bind9
### MTA

*postfix 

        apt-get install bind9

Serveur web :

*apache2

        apt-get install apache2
        
*php5.6

        sudo apt-get purge `dpkg -l | grep php| awk '{print $2}' |tr "\n" " "`
        sudo add-apt-repository ppa:ondrej/php
        sudo apt-get update
        sudo apt-get install php5.6
        sudo apt-get install php5.6-mbstring php5.6-mcrypt php5.6-mysql php5.6-xml
        sudo php -v
MDA
*dovecot

        apt-get install dovecot-common
        apt-get install dovecot-imapd dovecot-pop3d
MUA :
*Squirrelmail

        apt-get install squirrelmail
