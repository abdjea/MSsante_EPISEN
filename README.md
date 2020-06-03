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
bind9

        apt-get intall bind9
### Mail Transfert Agent

postfix 

        apt-get install bind9

### Serveur web :

apache2

        apt-get install apache2
        
php5.6

        sudo apt-get purge `dpkg -l | grep php| awk '{print $2}' |tr "\n" " "`
        sudo add-apt-repository ppa:ondrej/php
        sudo apt-get update
        sudo apt-get install php5.6
        sudo apt-get install php5.6-mbstring php5.6-mcrypt php5.6-mysql php5.6-xml
        sudo php -v
        
### Mail Delivery Agent
dovecot

        apt-get install dovecot-common
        apt-get install dovecot-imapd dovecot-pop3d
### Mail-tools 

Mail-tools

        apt-get install mailutils


### Mail User Agent
Squirrelmail

        apt-get install squirrelmail
        
## Service configuration - Way 1 : step by step

Use sudo to root privilege at each command 

### DNS

***Bind***

- `cd /etc/bind`
      
- `nano named.conf.local`
      
       zone "episen-sante.net" {
       type master;
       file "/etc/bind/db.episen";
      };
      zone "0.168.192.in-addr.arpa" {
       type master;
       file "/etc/bind/db.192";
      };
      
     
- `cp db.local db.episen`
- `cp db.127 db.192`     
- `nano db.episen`
      
      $TTL	604800
      @	IN	SOA	ns.episen-sante.net. root.episen-sante.net. (
               3		; Serial
          604800		; Refresh
           86400		; Retry
         2419200		; Expire
          604800 )	; Negative Cache TTL
      ;
      @	IN	NS	ns.episen-sante.net.
      @	IN	A	192.168.0.1
      ns	IN	A	192.168.0.1
       IN	MX 10	mail.episen-sante.net.
      www	IN	CNAME	ns
      mail	IN	CNAME	ns
      
      
      > nano db.192
      
      $TTL	604800
      @	IN	SOA	ns.episen-sante.net. root.episen-sante.net. (
               2		; Serial
          604800		; Refresh
           86400		; Retry
         2419200		; Expire
          604800 )	; Negative Cache TTL
      ;
      @	IN	NS	ns.episen-sante.net.
      1	IN	PTR	ns.episen-sante.net.
      
      >invoke-rc.d bind9 restart ?
      
- Configure the DNS of the host 
      
      > nano /etc/resolv.conf
      
      #modifiy the content of the file by these lines 
      search episen-sante.net
      servername 192.168.0.1

### Mail Transfert Agent - Postfix

       > dpkg-reconfigure postfix 
       
       Configuration type du serveur de messagerie :               Local uniquement (or Site Internet) ?
       Nom de courrier :                                           mail.episen-sante.net ?
       destinataire eds courriels de "root" et de "postmaster" :   mail.episen-sante.net, episen-sante.net
       Réseaux internes :                                          192.168.0.0/24
       Protocoles internet à utiliser :                            tous
       
       #add @mail
       > nano /etc/postfix/main.cf
       
       # Modify the following line by adding the domain name 
       mydestination = episen-sante.net

### Mail Delivery Agent - Dovecot

      > nano /etc/dovecot.conf

      # add the following line at the end of the file 
 
      protocols = imap pop3
      #disable_plaintext_auth = no
      mail_location = mbox :~/mail :INBOX=/var/mail/%u
      #mail_location = maildir :~/Maildir
 
### Mail User Agent - Squirrelmail 
 
       > nano /etc/squirremail/apache.cong
       
       # Uncomment line starting by Documentroot and ServerName to activate the virtual host
       # Modify webmail.exemple.com by your courrial name "mail.episen-sante.net" at the line ServerName ?
       
       > cp /etc/squirrelmail/apache.conf /etc/apache2/sites-available/squirrelmail.conf

       
## Service Configuration - Way 2 : download setup


