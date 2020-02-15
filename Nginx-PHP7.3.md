# Nginx Installation
In order to display web pages to our site visitors, we are going to employ Nginx, a modern, efficient web server.

## Step 01 -- Install nginx package

```shell
sudo apt update
sudo apt install nginx
```
After accepting the procedure, apt will install Nginx and any required dependencies to your server.

## Step 02 -- Firewall configure

After install Nginx, the firewall software needs to be adjusted to allow access to the service. Nginx registers itself as a service with **ufw** upon installation, making it straightforward to allow Nginx access.

List the application configurations that **ufw** knows how to work with by typing:

```shell
sudo ufw app list
```

You should get a listing of the application profiles:

```text
Output:

Available applications:
  Nginx Full
  Nginx HTTP
  Nginx HTTPS
  OpenSSH
```
As you can see, there are three profiles available for Nginx:

<ul>
<li>
Nginx Full: This profile opens both port 80 (normal, unencrypted web traffic) and port 443 (TLS/SSL encrypted traffic).
</li>

<li>
Nginx HTTP: This profile opens only port 80 (normal, unencrypted web traffic).
</li>

<li>
Nginx HTTPS: This profile opens only port 443 (TLS/SSL encrypted traffic).
</li>
</ul>

It is recommended that you enable the most restrictive profile that will still allow the traffic you’ve configured. Since we will configure SSL for our server in this guide, we will only need to allow traffic on port 443.

You can enable this by typing:

```shell
sudo ufw allow 'Nginx HTTPS'
```

You can verify the change by typing:

```shell
sudo ufw status
```

You should see HTTP traffic allowed in the displayed output:

```text
Output:

Status: active

To                         Action      From
--                         ------      ----
OpenSSH                    ALLOW       Anywhere                  
Nginx HTTP                 ALLOW       Anywhere                  
OpenSSH (v6)               ALLOW       Anywhere (v6)             
Nginx HTTP (v6)            ALLOW       Anywhere (v6)
```

## Step 03 -- Server root directory setup

Nginx on Ubuntu 18.04 has one server block enabled by default that is configured to serve documents out of a directory at **/var/www/html**. While this works well for a single site, it can become unwieldy if you are hosting multiple sites. Instead of modifying **/var/www/html**, let’s create a directory structure within **/var/www** for our **example.com** site, leaving **/var/www/html** in place as the default directory to be served if a client request doesn’t match any other sites.

Create the directory for **example.com** as follows, using the **-p** flag to create any necessary parent directories:

```shell
sudo mkdir /var/www/your_domain
```
Next, assign current user as root web directory owner and read only permisson to nginx www-data group. $USER is environment variable, which should reference your current system user.

```shell
sudo chown -R $USER /var/www/your_domain
sudo chgrp -R www-data /var/www/your_domain
sudo chmod -R 750 /var/www/your_domain
sudo chmod g+s /var/www/your_domain

# If need nginx write permission for a folder. Ex: uploads
sudo chmod g+w /var/www/your_domain/uploads

# To check permission
ls -l
```

To avoid a possible hash bucket memory problem that can arise from adding additional server names, it is necessary to adjust a single value in the <code>/etc/nginx/nginx.conf</code> file. Open the file:

```shell
sudo nano /etc/nginx/nginx.conf
```
Find the **server_names_hash_bucket_size** directive and remove the **#** symbol to uncomment the line:

```text
…
http {
    …
    server_names_hash_bucket_size 64;
    …
}
…
```

Next, test to make sure that there are no syntax errors in any of your Nginx files:

```shell
sudo nginx -t
```

Save and close the file when you are finished.

If there aren’t any problems, restart Nginx to enable your changes:

```shell
sudo systemctl reload nginx
```


# PHP 7.3 Installation

PHP 7.3 for Ubuntu and Debian is available from ondrej/php PPA repository. PHP 7.3 stable version has been released with many new features and bug fixes.

## Step 1: Add PHP 7.3 PPA
Add ondrej/php which has PHP 7.3 package and other required PHP extensions.


```shell
sudo add-apt-repository ppa:ondrej/php
sudo apt-get update
```
This PPA can be added to your system manually by copying the lines below and adding them to your system’s software sources.

## Step 2: Install PHP 7.3
Once the PPA repository has been added, install php 7.3 on your Ubuntu/Debian.

```shell
sudo apt-get install php7.3
```

Check version installed

```shell
php -v
```

## Step 3: Installing PHP 7.3 Extensions
Install PHP 7.3 extensions by using the syntax.

```shell
sudo apt-get install php7.3-<entension-name>
```

Install common php extensions

```shell
sudo apt install php7.3-common php7.3-cli php7.3-fpm php7.3-json php7.3-pdo php7.3-mysql php7.3-zip php7.3-gd  php7.3-mbstring php7.3-curl php7.3-xml php7.3-bcmath php7.3-json
```

# Configuring Nginx to Use the PHP Processor
You now have Nginx installed to serve your pages. However, you still don’t have anything that can generate dynamic content. This is where PHP comes into play.

Since Nginx does not contain native PHP processing like some other web servers, you will need to install php-fpm, which stands for “fastCGI process manager”. We will tell Nginx to pass PHP requests to this software for processing.

This is done on the server block level (server blocks are similar to Apache’s virtual hosts). To do this, open a new server block configuration file within the <code>/etc/nginx/sites-available/</code> directory. In this example, the new server block configuration file is named <code>example.com</code>, although you can name yours whatever you’d like:

```shell
sudo nano /etc/nginx/sites-available/your_domain
```
By editing a new server block configuration file, rather than editing the default one, you’ll be able to easily restore the default configuration if you ever need to.

Add the following content, which was taken and slightly modified from the default server block configuration file, to your new server block configuration file:

```nginx
server {
        listen 80;
        root /var/www/your_domain;
        index index.php index.html index.htm index.nginx-debian.html;
        server_name example.com;

        location / {
                try_files $uri $uri/ =404;
        }

        location ~ \.php$ {
                include snippets/fastcgi-php.conf;
                fastcgi_pass unix:/var/run/php/php7.3-fpm.sock;
        }

        location ~ /\.ht {
                deny all;
        }
}
```

**For Laravel App host** [https://laravel.com/docs/master/deployment#nginx](https://laravel.com/docs/master/deployment#nginx)

```nginx
server {
    listen 80;
    server_name example.com;
    root /var/www/your_domain/public;

    add_header X-Frame-Options "SAMEORIGIN";
    add_header X-XSS-Protection "1; mode=block";
    add_header X-Content-Type-Options "nosniff";

    index index.html index.htm index.php;

    charset utf-8;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location = /favicon.ico { access_log off; log_not_found off; }
    location = /robots.txt  { access_log off; log_not_found off; }

    error_page 404 /index.php;

    location ~ \.php$ {
        fastcgi_pass unix:/var/run/php/php7.3-fpm.sock;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
        include fastcgi_params;
    }

    location ~ /\.(?!well-known).* {
        deny all;
    }
}
```

**HTTPS Laravel App**

```nginx
server {
    listen 80;
    listen [::]:80;
    server_name example.com www.example.com;
    return 302 https://$server_name$request_uri;
}

server {

    # SSL configuration

    listen 443 ssl http2;
    listen [::]:443 ssl http2;
    ssl_certificate         /etc/ssl/certs/your_domain/cert.pem;
    ssl_certificate_key     /etc/ssl/private/your_domain/key.pem;
    ssl_client_certificate  /etc/ssl/certs/your_domain/cloudflare.crt;
    ssl_verify_client on;

    server_name example.com www.example.com;

    root /var/www/your_domain/public;
    index index.php index.html index.htm;

    charset utf-8;
    
    add_header X-Frame-Options "SAMEORIGIN";
    add_header X-XSS-Protection "1; mode=block";
    add_header X-Content-Type-Options "nosniff";


    location / {
            try_files $uri $uri/ /index.php?$query_string;
    }
    
    error_page 404 /index.php;

    location ~ \.php$ {
            include snippets/fastcgi-php.conf;
            fastcgi_pass unix:/var/run/php/php7.3-fpm.sock;
            fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
            include fastcgi_params;
    }

    location ~ /\.(?!well-known).* {
            deny all;
    }
}
```

Here’s what each of these directives and location blocks do:

<ul>
<li>
<code>listen</code> — Defines what port Nginx will listen on. In this case, it will listen on port 80, the default port for HTTP.
</li>
<li>
<code>root</code> — Defines the document root where the files served by the website are stored.
</li>
<li>
<code>index</code> — Configures Nginx to prioritize serving files named index.php when an index file is requested, if they’re available.
</li>
<li>
<code>server_name</code> — Defines which server block should be used for a given request to your server. Point this directive to your server’s domain name or public IP address.
</li>
<li>
<code>location /</code> — The first location block includes a try_files directive, which checks for the existence of files matching a URI request. If Nginx cannot find the appropriate file, it will return a 404 error.
</li>
<li>
<code>location ~ \.php$</code> — This location block handles the actual PHP processing by pointing Nginx to the fastcgi-php.conf configuration file and the php7.2-fpm.sock file, which declares what socket is associated with php-fpm.
</li>
<li>
<code>location ~ /\.ht</code> — The last location block deals with .htaccess files, which Nginx does not process. By adding the deny all directive, if any .htaccess files happen to find their way into the document root they will not be served to visitors.
</li>
</ul>

After adding this content, save and close the file. Enable your new server block by creating a symbolic link from your new server block configuration file (in the <code>/etc/nginx/sites-available/</code> directory) to the <code>/etc/nginx/sites-enabled/</code> directory:

```shell
sudo ln -s /etc/nginx/sites-available/your_domain /etc/nginx/sites-enabled/
```

Then, unlink the default configuration file from the /sites-enabled/ directory:

```shell
sudo unlink /etc/nginx/sites-enabled/default
```

Test your new configuration file for syntax errors by typing:

```shell
sudo nginx -t
```

If any errors are reported, go back and recheck your file before continuing.

When you are ready, reload Nginx to make the necessary changes:

```shell
sudo systemctl reload nginx
```

# Test PHP Nginx

Your nginx and php are ready now. You can test it to validate that Nginx can correctly hand .php files off to the PHP processor.

To do this, use your text editor to create a test PHP file called info.php in your document root:

```shell
sudo nano /var/www/example.com/html/info.php
```

Enter the following lines into the new file. This is valid PHP code that will return information about your server:

```php
<?php
phpinfo();
```

When you are finished, save and close the file.

Now, you can visit this page in your web browser by visiting your server’s domain name or public IP address followed by <code>/info.php</code>:

```url
http://your_server_domain_or_IP/info.php
```

You should see a web page that has been generated by PHP with information about your server:


![img](https://i.imgur.com/aHq0LwZ.png)


If you see a page that looks like this, you’ve set up PHP processing with Nginx successfully.

For now, remove the file by typing:

```shell
sudo rm /var/www/your_domain/info.php
```
