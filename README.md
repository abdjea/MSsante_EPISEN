# Mssante_EPISEN

As part of a medical informatics project, we decided to set up an "MSSanté" messaging server meeting health standards in France.

 > "MSSanté" is an electronic messaging system reserved for healthcare professionals.

In this work , we have configured these following network components :

| Service | Package/value | State |
| :---  | :---:  | :---:  |
| **Domain name** | episen-sante.net | :white_check_mark: |
| **DNS**  | Bind9 | :white_check_mark: |
| **Mail Tranfert Agent**  | Postfix (SMTP)| :white_check_mark: |
| **Mail Delevery Agent**  | Dovecot (IMAP, POP3) | :white_check_mark: |
| **Mail User Agent**  | Squirrelmail | :white_check_mark: |



 > The "MSSanté" system consists of a “trusted space” that can be integrated by messaging operators (healthcare establishment, software publisher, administration, etc.) to secure the electronic messaging service they offer to healthcare professionals.
 
Work done/to do : 

- [x] Paramount service configured
- [ ] Bring the application into compliance
 

## 1 - Environment setup
Ubuntu 16.04 is required to install the package "squirrelmail" using an Apt-based PackageManagement tool.Furthermore, Squirrelmail required php5 which is more easy to install in the verision 16.04 of ubuntu.

### 1.1 - Virtualbox Network configuration

- `Click on "Host Network Manager > Create"`

***in the tab "Interface"***

- `Select the case "configure the card manually"`
        
        @IPv4                           :   192.168.0.100
        Masque réseau @IPv4             :   255.255.255.0
        
***in the tab "DHCP Server"***

        @IPv4 du serveur                :   192.168.0.100
        Masque réseau @IPv4             :   255.255.255.0
        Limite inférieur des adresses   :   192.168.0.1
        Limite supérieur des adresses   :   192.168.0.10
        
 - `Click on "Apply" to save the current configuration`


### 1.2 - Ubuntu 16.04 LTS

***Recover the display capabilities of  the virtual machine on the host***

        sudo apt install build-essential dkms linux-headers-$(uname -r)
- `add Virtual Guest Additions # via Vbox windows`

***Update the system***

        sudo apt-get update
        sudo apt-get upgrade


## 2 - Service installation 

### 2.1 - Serveur DNS - Bind9

        sudo apt-get intall bind9
### 2.2 - Mail Transfert Agent - Postfix 

        sudo apt-get install bind9

### 2.3 - Serveur web

***apache2***

        sudo apt-get install apache2
        
***php5.6***

        sudo apt-get purge `dpkg -l | grep php| awk '{print $2}' |tr "\n" " "`
        sudo add-apt-repository ppa:ondrej/php
        sudo apt-get update
        sudo apt-get install php5.6
        sudo apt-get install php5.6-mbstring php5.6-mcrypt php5.6-mysql php5.6-xml
        sudo php -v
        
### 2.4 - Mail Delivery Agent - Dovecot

        sudo apt-get install dovecot-common
        sudo apt-get install dovecot-imapd dovecot-pop3d
### 2.5 - Mail-tools - Mailutils (optional) 

        sudo apt-get install mailutils


### 2.6 - Mail User Agent - Squirrelmail

        sudo apt-get install squirrelmail
        
## 3 - Service configuration - Way 1 : Step by step (Verified)

`Use sudo to root privilege at each command`

### 3.1 - DNS

***Bind***

- `sudo cd /etc/bind`
      
- `sudo nano named.conf.local`
      
       zone "episen-sante.net" {
       type master;
       file "/etc/bind/db.episen";
      };
      zone "0.168.192.in-addr.arpa" {
       type master;
       file "/etc/bind/db.192";
      };
      
     
- `sudo cp db.local db.episen`
- `sudo cp db.127 db.192`     
- `sudo nano db.episen`
      
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
      
      
- `sudo nano db.192`
      
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
      
- `sudo invoke-rc.d bind9 restart`
      
***Configure the DNS of the host*** 
      
- `sudo nano /etc/resolv.conf`
      
      #modifiy the content of the file by these lines 
      search episen-sante.net
      servername 192.168.0.1

### 3.2 - Mail Transfert Agent - Postfix

- `sudo dpkg-reconfigure postfix`
       
       Configuration type du serveur de messagerie                 :           Site Internet
       Nom de courrier                                             :           episen-sante.net 
       destinataire des courriels de "root" et de "postmaster"     :           mail.episen-sante.net, episen-sante.net
       Autres destinations pour lesquelles le courier ser accepté  :           episen-sante.net
       Faut-il forcer les mise à jour synchronisées de la file
       d'attente des courriels                                     :           Non
       Réseaux internes                                            :           192.168.0.0/24
       Taille maximale des boites aux lettres                      :           0
       Caractère d'extension des addresses locales                 :           (champ vide)
       Protocoles internet à utiliser                              :           Tous
       

- `sudo nano /etc/postfix/main.cf`
       
       # Modify the following line by adding the domain name 
       mydestination = episen-sante.net

### 3.3 - Mail Delivery Agent - Dovecot

- `sudo nano /etc/dovecot.conf`

      # add the following line at the end of the file 
      protocols = imap pop3
      #disable_plaintext_auth = no
      mail_location = mbox :~/mail :INBOX=/var/mail/%u
      #mail_location = maildir :~/Maildir
 
### 3.4 - Mail User Agent and Web Server - Squirrelmail and Apache2
 
- `sudo nano /etc/squirremail/apache.conf`
       
       # Uncomment line starting by Documentroot and ServerName to activate the virtual host
       # Modify webmail.exemple.com by your courrial name "mail.episen-sante.net" at the line ServerName
       
       #<VirtualHost 1.2.3.4>
         DocumentRoot /usr/share/squirrelmail
         ServerName mail.episen-sante.net
       #</VirtualHost>

- `sudo cp /etc/squirrelmail/apache.conf /etc/apache2/sites-available/squirrelmail.conf`
- `sudo a2ensite squirrelmail.conf`
       
## 4 - Service Configuration - Way 2 : Download setup (Not verified)

This way of configuration consists to copy/paste exiting files in the system.

- Follow the section `3.2 - Mail Transfer Agent - Postfix`
- Download the reposoterie in .rar
   - ***In the terminal***


           unrar MSsante_EPISEN`
           cd MSsante_EPISEN`
           cp etc /etc
## 5 - Utilisation

***Portail d'authentification***

- Type in the address bar of your browser the domain name concanate to the Squirrellmail path : `episen-sante.net/squirrelmail`

***Create an new email account***

- Add an new user : `sudo adduser XX` (XX : new account name)

***Join a domain***
- when to join :
  - if you add an new host 
  - if you restart the host (or `sudo service network-manager stop`)
  
- Follow the section `3.1 - DNS - Configure the DNS of the host`
