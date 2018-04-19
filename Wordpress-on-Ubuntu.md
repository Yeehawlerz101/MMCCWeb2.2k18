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
Prerequisites:
>Working VirtualBox installation.

>Bootable Ubuntu 14.04 Server ISO.
* Create a new VM
* Create Ubuntu 64bit and use all recommendations.
 ###### Before starting Click settings Go to Network section Switch from NAT -> Bridged Adaptor

* Start the VM
* Install Ubuntu
* When prompted, connect to the ISO you downloaded
* If your keyboard/mouse are captured you can use the Command/Option keys 
* Hit enter (most default settings will work for now)
* Create your user account “User123” and “user123” password will be "password".
* Don’t encrypt your home directory
* Use "Guided-use entire disk and setup LVM"
* Arrow over to Yes to go with recommendations
* Hit enter to accept the allocated size
* Arrow to Yes to write changes to disk
* Do not select proxy settings
* Select the OpenSSH package **with the space bar** (we can use this with putty)
* Select and allow the GRUB Bootloader
* Login and run the following commands
```
sudo apt-get dist-upgrade
ifconfig
sudo apt-get update
sudo apt-get install apache2
sudo apache2ctl configtest
```
* Open up the main configuration file with your text editor with:
```
sudo nano /etc/apache2/apache2.conf
```
* Enter this following line at the end of the config file.
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
* You now should be able to view the base HTML from apache by visiting your VM's IP address or domain.
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
sudo apt-get install php libapache2-mod-PHP PHP-mcrypt PHP-MySQL
```
* Edit the config to make php load first rather than the HTML version.
```
sudo nano /etc/apache2/mods-enabled/dir.conf
```
* Remove **All** of the content inside of the file and paste this:
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
* Install PHP modules that help in getting wordpress setup
```
sudo apt-get install php-curl php-gd php-mbstring php-mcrypt php-xml php-xmlrpc
sudo systemctl restart apache2
```
## Apache's Configuration to Allow for .htaccess 
* Open the main config for apache
```
sudo nano /etc/apache2/apache2.conf
```
* Then paste this at the bottom of the config
```
<Directory /var/www/html/>
    AllowOverride All
</Directory>
```
* Enable Permalinks setup by wordpress
```
sudo a2enmod rewrite
sudo systemctl restart apache2
```
* Test your progress so far by using the following command: `sudo apache2ctl configtest`, if theres an error review your commands by pressing the up arrow on the keyboard.
## Actually Get Wordpress
###### You're almost done!
* Download the current version of Wordpress but first you must change directory to the "tmp" folder
```
cd /tmp
curl -O https://wordpress.org/latest.tar.gz
```
* Extract the compressed folder
```
tar xzvf latest.tar.gz
```
* Create the file and set the permissions by pasting:
```
touch /tmp/wordpress/.htaccess
chmod 660 /tmp/wordpress/.htaccess
```
* Now let's copy over the default settings that Wordpress needs
``` 
cp /tmp/wordpress/wp-config-sample.php /tmp/wordpress/wp-config.php
```
* Then we can psuh all of the files into the html diredctory
```
sudo cp -a /tmp/wordpress/. /var/www/html
```
## Configurating the WordPress Directory
* Set a shared owner with the user **Note:** where user123 is is where your username should be.
```
sudo chown -R user123:www-data /var/www/html
sudo find /var/www/html -type d -exec chmod g+s {} \;
sudo chmod g+w /var/www/html/wp-content
sudo chmod -R g+w /var/www/html/wp-content/themes
sudo chmod -R g+w /var/www/html/wp-content/plugins
```
## WordPress Configuration File settings
* A salt is to just get a unique ID like a license plate on a car or a Callsign in the military. We need one here since Wordpress is Salty :P
```
curl -s https://api.wordpress.org/secret-key/1.1/salt/
```
* You'll get results that are unique to **Your** request. here is what I got incase you're too lazy to find or get your own.
```
define('AUTH_KEY',         '|C_+`s?l?*K|4Rj;&^-v;MHrF=qbjeF%ag-9=A4u+jPMv95)(m4heWP+KFiKl}{L');
define('SECURE_AUTH_KEY',  'EI|e53`#k>)B|GJMkCWkyw/4cjWict@Eg-v3e_{s%ty}u+{/wua98W*=eD]Hb! 8');
define('LOGGED_IN_KEY',    '1SxU?+*NT+V4k0-^j@pVExcv;4w7M]+S*a2ODZ{(`m 9:g9.x?A7zJt0GYHHf+}4');
define('NONCE_KEY',        '@zB*~|Jp<*_cO A]QuLN|%-o9rjQ}BAW$X|+MXVdWQo_ce1KgzHsdq44K1NhXO_W');
define('AUTH_SALT',        'g]txj+gT4Mj$Y-(Y-+ZEHI0PK?q{uRmb#<]{>II<c@}K` wT@|[k,d nE=J9~Y%)');
define('SECURE_AUTH_SALT', ':`g9f+)Xk4 4,)>2zY2RL(=g9Cw>9MhwEM:eFUUL{+6nehoQJO1_xaK~|-.fL^dQ');
define('LOGGED_IN_SALT',   '?E6b]caz!Hk0=}tUXE^sY+Z-5:n7Hq68NpYp@S^08JA%.hM]i#)Y-wh~s{jChPV/');
define('NONCE_SALT',       '0W:<{%n^=cCk1sVC_S)d@U|($!q8#*e}4z$jbFi,n>sbG+Ta.YSJq#$Pt4<Un&e&');
```
* Now open the config for Wordpress again
```
nano /var/www/html/wp-config.php
```
* And replace them with the current existing ones.
* Next place the Database connection settings inside of the pre existing commands.
```
define('DB_NAME', 'wordpress');
define('DB_USER', 'wordpressdude');
define('DB_PASSWORD', 'password');
define('FS_METHOD', 'direct');
```
## Complete the Installation
* Visit `http://server_domain_or_IP` and you should see your new wordpress site. :D

## Credits to Digital Ocean
###### Taken From their [instructions](https://www.digitalocean.com/community/tutorials/how-to-install-linux-apache-mysql-php-lamp-stack-on-ubuntu-16-04).
###### The last part is from their [instructions](https://www.digitalocean.com/community/tutorials/how-to-install-wordpress-with-lamp-on-ubuntu-16-04) here.
