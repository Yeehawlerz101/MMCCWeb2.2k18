# How to get Wordpress working on Ubuntu Notes
##### Created By Neil Master
###### This is for Ubuntu 14.04 and Currently works on 4/3/18
## Simple Unix Controls for setup
Useful keyboard buttons:  
* Spacebar (for selecting)
* Tab (for moving between fields)
* Arrow keys (for moving between fields and/or within a text field)
* Escape (for going back)
* Enter (for going forward)
---
* Install VirtualBox
* Create a new VM
* Create Ubuntu 64bit and use all recommendations (you may want to use Expert mode to save to the thawspace)
* Before starting…
* Click settings
* Go to Network section
* Switch from NAT to Bridged Adaptor

* Start the VM
* Install Ubuntu
* When prompted, connect to the ISO you downloaded
* If your keyboard/mouse are captured you can use the Command/Option keys 
* Hit enter (most default settings will work for now)
* Create your user account “User123” and “user123” password will be "password".
* Don’t encrypt your home directory
* Use "Guided - use entire disk and setup LVM"
* Arrow over to Yes to go with recommendations
* Hit enter to accept the allocated size
* Arrow to Yes to write changes to disk
* Do not select proxy settings
* Select the OpenSSH package **with the space bar** (we can use this with putty)
* Select and allow the GRUB Boot loader
* Login and run the following commands
```
sudo apt-get dist-upgrade
ifconfig
sudo apt-get update
sudo apt-get install apache2
sudo apache2ctl configtest
```
* Open up the main configuration file with your text editor with 
```
sudo nano /etc/apache2/apache2.conf
```
* Enter this following line at the end of the config file
```
ServerName "IP-Address-or-Domain-Goes-Here-Without-Quotes"
```
* Save and close the file when you are finished. Next, check for syntax errors by typing:
```
sudo apache2ctl configtest
```
* The output should be: **Syntax OK**
* Now its time to restart the apache service.
```
sudo systemctl restart apache2
```
* Adjust the Firewall to Allow Web Traffic
```
sudo ufw allow in "Apache Full"
```
* You now should be able to view the base html from apache from visiting your VM's IP address or domain.
``` http://your_server_IP_address```
## Installing MySQL
```
sudo apt-get install mysql-server
```
* Enable secure settings for users.
```
mysql_secure_installation
```
* Create use the same or a new password for MySQL
```
y, no, no, y
```
## Installing PHP
```
sudo apt-get install php libapache2-mod-php php-mcrypt php-mysql
```
* Edit the config to make php load first rather than the html version.
```
sudo nano /etc/apache2/mods-enabled/dir.conf
```
* Remove **All** of the content inside of the file and paste this
```
<IfModule mod_dir.c>
    DirectoryIndex index.php index.html index.cgi index.pl index.xhtml index.htm
</IfModule>
```
* Now restart Apache
```
sudo systemctl restart apache2
```
### Testing our PHP 
* Goto the default php file and add 3 lines
```
sudo nano /var/www/html/info.php
```
* Type 
```
<?php
phpinfo();
?>
```
* Now test if the PHP installation is working correctly 
`http://your_server_IP_address/info.php`
## Create a Database for WordPress
```
mysql -u root -p
```
* Lets create a database called "wordpress"
```
CREATE DATABASE wordpress DEFAULT CHARACTER SET utf8 COLLATE utf8_unicode_ci;
```
* Now lets make it so there is a user with the name of "wordpressdude" that has the password as "password"
```
GRANT ALL ON wordpress.* TO 'wordpressdude'@'localhost' IDENTIFIED BY 'password';
```
* Use the flush command to make MySQL read the new changes.
```
FLUSH PRIVILEGES;
```
* Exit the MySQL syntax
```
EXIT;
```
## Credits to Digital Ocean
###### Taken From their [instructions](https://www.digitalocean.com/community/tutorials/how-to-install-linux-apache-mysql-php-lamp-stack-on-ubuntu-16-04).
