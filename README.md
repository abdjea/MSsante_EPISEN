# Mssante_EPISEN
Bind9,postfix,dovecot,Squirrelmail

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

recover the display capabilities of the host on the virtual machine :

sudo apt install build-essential dkms linux-headers-$(uname -r)
add Virtual Guest Additions

update the system :

apt-get update
apt-get upgrade


