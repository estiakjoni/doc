Shopware is an open source eCommerce platform for online businesses. This guide will show you how to install Shopware Community Edition (CE) on a fresh Ubuntu 18.04 LTS server instance.

# Requirements
Minimum requirements of Shopware are:

* PHP version 5.6.4 or above with the following extensions:
 * ctype
 * curl
 * dom
 * hash
 * iconv
 * gd (with freetype and libjpeg)
 * json
 * mbstring
 * OpenSSL
 * session
 * SimpleXML
 * xml
 * zip
 * zlib
 * PDO/MySQL
* Nginx or Apache with <code>mod_rewrite</code> enabled. This guide will use Nginx.
* MySQL version 5.5.0 or above
* IonCube Loader version 5.0 optional, but recommended

# Before you begin
Check the Ubuntu version.
```shell
lsb_release -ds
```
Create a new non-root user account with sudo access and switch to it.
NOTE: Replace tankibaj with your username.
```
adduser shopware --gecos "Shop Ware"
usermod -aG sudo shopware
su - shopware
```
Set up the timezone.
```
sudo dpkg-reconfigure tzdata
```
Ensure that your system is up to date.
```
sudo apt update && sudo apt upgrade -y
```
Install unzip and curl.
```
sudo apt install -y unzip curl
```

# Install PHP
Install PHP 7.3 and required PHP extensions.
```
sudo add-apt-repository ppa:ondrej/php
sudo apt-get update
sudo apt install -y php7.3 php7.3-cli php7.3-fpm php7.3-common php7.3-mysql php7.3-curl php7.3-json php7.3-zip php7.3-gd php7.3-xml php7.3-mbstring php7.3-opcache
```
Check the version.
```
php --version
```
# Install IonCube Loader
```
cd /tmp && wget https://downloads.ioncube.com/loader_downloads/ioncube_loaders_lin_x86-64.tar.gz
```
Extract the loader.
```
tar xfz ioncube_loaders_lin_*.gz
```
Find the PHP extensions directory on the system by running the commands below.
```
php -i | grep extension_dir
# extension_dir => /usr/lib/php/20180731 => /usr/lib/php/20180731
```
Copy the ionCube Loader into the PHP extensions directory.
```
sudo cp /tmp/ioncube/ioncube_loader_lin_7.3.so /usr/lib/php/20180731
```
Include the loader via PHP configuration.
```
sudo nano /etc/php/7.3/fpm/php.ini
```
Then, add a line in the file to include ionCube loader. It can be anywhere in the file below the [PHP] line.
```
zend_extension = /usr/lib/php/20180731/ioncube_loader_lin_7.3.so

```
Save the file and restart PHP-FPM.
```
sudo systemctl restart php7.3-fpm.service
```
# Install MySQL and setup the database
Install MySQL.
```
cd /tmp && curl -OL https://repo.mysql.com//mysql-apt-config_0.8.13-1_all.deb
sudo dpkg -i mysql-apt-config* && sudo apt update
rm mysql-apt-config*
sudo apt install mysql-server
sudo systemctl status mysql
```
Check the version.
```
mysql --version && sudo mysqld --version
```
Run mysql_secure installation to improve MySQL security and set the password for the MySQL root user.
```
sudo mysql_secure_installation

# Would you like to setup VALIDATE PASSWORD plugin? N
# Please set the password for root here.
# New password: **********************
# Re-enter new password: **********************
# Remove anonymous users? Y
# Disallow root login remotely? Y
# Remove test database and access to it? Y
# Reload privilege tables now? Y

# Success.

# All done!
```
Connect to the MySQL shell as the root user.
```
sudo mysql -u root -p
# Enter password
```
Create an empty MySQL database and user for Shopware, and remember the credentials.
```
CREATE DATABASE shopwaredb;
CREATE USER 'shopware'@'%' IDENTIFIED WITH mysql_native_password BY 'password';
GRANT ALL ON shopwaredb.* TO 'shopware';
FLUSH PRIVILEGES;
EXIT;
```
