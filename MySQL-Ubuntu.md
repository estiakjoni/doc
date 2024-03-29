## Installing MySQL
Now that you have your web server up and running, it is time to install MySQL. MySQL is a database management system. Basically, it will organize and provide access to databases where your site can store information.

<ul>

<li>Use apt to acquire and install this software:

```bash
sudo apt update
sudo apt install mysql-server
```
</li>


<li>When the installation is complete, run a simple security script that comes pre-installed with MySQL which will remove some dangerous defaults and lock down access to your database system. Start the interactive script by running:

```bash
sudo mysql_secure_installation
```
</li>

<li>Open up the MySQL prompt from your terminal:

```bash
sudo mysql -u root -p
```
</li>

<li>Next, check which authentication method each of your MySQL user accounts use with the following command:

<pre>SELECT user,authentication_string,plugin,host FROM mysql.user;</pre>
</li>


<li>In this example, you can see that the root user does in fact authenticate using the auth_socket plugin. To configure the root account to authenticate with a password, run the following ALTER USER command. Be sure to change password to a strong password of your choosing:

<pre>ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '123123';</pre>
</li>


<li>Then, run FLUSH PRIVILEGES which tells the server to reload the grant tables and put your new changes into effect:
<pre>FLUSH PRIVILEGES;</pre>
</li>


<li>Once you confirm this on your own server, you can exit the MySQL shell:
  <pre>exit</pre>
</li>

</ul>


##  Installing phpMyAdmin
<ul>

<li>To get started, we will install phpMyAdmin from the default Ubuntu repositories.
  
  ```bash
sudo apt install phpmyadmin php-mbstring php-gettext
  ```
</li>


<li>The installation process adds the phpMyAdmin Apache configuration file into the /etc/apache2/conf-enabled/ directory, where it is read automatically. The only thing you need to do is explicitly enable the mbstring PHP extension, which you can do by typing:
  
  ```bash
  sudo phpenmod mbstring
  ```
  </li>
  
  <li>Afterwards, restart Apache for your changes to be recognized:
  
  ```bash
  sudo systemctl restart apache2
  ```
  </li>
  
<li>Adjusting User Authentication and Privileges, To do this, open up the MySQL prompt from your terminal:
  
  ```bash
  sudo mysql -u root -p
  update mysql.user set plugin='' where user='root';
  flush privileges;
  exit
  ```
</li>  

</ul>



## Uninstall MySQL

```bash
sudo dpkg-reconfigure mysql-server-N.N
sudo apt-get --purge remove mysql-server mysql-common mysql-client
```
(where N.N is the MySql Server version)
