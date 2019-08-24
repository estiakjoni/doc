## Install PHP

```bash
sudo apt -y install php php*-cgi libapache2-mod-php* php*-common php*-pear php*-mbstring php*-xml php*-gd php*-opcache php*-fpm php*-zip php*-curl php*-json php*-bcmath php*-ctype php*-pdo php*-tokenizer php**-mcrypt php*-readline php*-cli
```

## Configure Apache2

```bash
sudo a2enconf php*-cgi
```


## Configure php date time zone
Edit php.ini to setup time zone

```bash
sudo nano /etc/php/7.2/apache2/php.ini
```
# line 941: uncomment and add your timezone
<pre>
date.timezone = "Europe/Berlin"
</pre>



## Configure PHP-FPM (Optional)
<ul>
<li>Open VirtualHost to Edit

```bash
sudo nano /etc/apache2/sites-available/000-default.conf
```
</li>

<li>add into <VirtualHost> - </VirtualHost>

```text
<FilesMatch \.php$>
	SetHandler "proxy:unix:/var/run/php/php7.2-fpm.sock|fcgi://localhost/"
</FilesMatch>
```
</li>

<li>Enabling proxy_fcgi and setenvif

```bash
sudo a2enmod proxy_fcgi setenvif
```
</li>

<li>Enabling conf php7.2-fpm

```bash
a2enconf php7.2-fpm
```
</li>


<li>Configure php fpm date time zone

Open current loaded php.ini 

```bash
sudo nano /etc/php/7.2/fpm/php.ini 
```

Change following line
<pre>
date.timezone = "Europe/Berlin"
</pre>

</li>


<li> Restart and Reload php7.2-fpm and apache2

```bash
systemctl reload apache2
sudo systemctl restart php7.2-fpm apache2
```
</li>
</ul>


## Test php
Create [phpinfo] in Virtualhost's web-root:

```bash
echo '<?php phpinfo(); ?>' > /var/www/html/info.php
```
Hit: http://localhost/info.php


# Oprional For PHP

## Show the List of Installed Packages on Ubuntu or Debian
<ul>
<li>The command we need to use is dpkg –get-selections, which will give us a list of all the currently installed packages.

```bash
dpkg --get-selections
```
</li>
	
<li>The full list can be long and unwieldy, so it’s much easier to filter through grep to get results for the exact package you need. For instance, I wanted to see which php packages I had already installed through apt-get:

```bash
dpkg --get-selections | grep php
```
</li>

<li>For extra credit, you can find the locations of the files within a package from the list by using the dpkg -L command, such as:

```bash
dpkg -L php7.2-gd
```
</li>
</ul>

