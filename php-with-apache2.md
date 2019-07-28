## Install PHP

```bash
sudo apt -y install php php-cgi libapache2-mod-php php-common php-pear php-mbstring php-xml php-gd php-opcache php-fpm
```

## Configure Apache2

```bash
sudo a2enconf php7.2-cgi
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
