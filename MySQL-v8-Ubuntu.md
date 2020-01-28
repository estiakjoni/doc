# Adding the MySQL Software Repository
Now we’re going to download the repository.

```shell
cd /tmp && curl -OL https://repo.mysql.com//mysql-apt-config_0.8.13-1_all.deb
```

Now we’re ready to install. Note that in the package installation process, you will be prompted to choose MySQL server version and other components such as cluster, shared client libraries, or the MySQL workbench that you want to configure for installation.

MySQL server version mysql-8.0 will be auto-selected, then scroll down to the last option <code>Ok</code> and click <code>Enter</code> to finish the configuration and installation of the release package, as shown in the screenshot.

```shell
sudo dpkg -i mysql-apt-config*
```

The package will now finish adding the repository. Refresh your apt package cache to make the new software packages available:

```shell
sudo apt update
```

Let’s also clean up after ourselves and delete the file we downloaded:

```shell
rm mysql-apt-config*
```

# Installing MySQL
Having added the repository and with our package cache freshly updated, we can now use apt to install the latest MySQL server package:

```shell
sudo apt install mysql-server
```
MySQL should now be installed and running. Let’s check using systemctl

```shell
sudo systemctl status mysql
```

# Securing MySQL
MySQL comes with a command we can use to perform a few security-related updates on our new install. Let’s run it now:

```shell
sudo mysql_secure_installation

# Would you like to setup VALIDATE PASSWORD plugin? N
# Please set the password for root here.
# New password: **********************
# Re-enter new password: **********************
# Remove anonymous users? Y
# Disallow root login remotely? N
# Remove test database and access to it? Y
# Reload privilege tables now? Y

# Success.

# All done!
```
Connect to the MySQL shell as the root user.

```bash
sudo mysql -u root -p
# Enter password
```

Next, check which authentication method each of your MySQL user accounts use with the following command:

```shell
SELECT user,authentication_string,plugin,host FROM mysql.user;
```

MySQL 8 default authentication plugin is caching_sha2_password. To change root user plugin to mysql_native_password, run the following ALTER USER command. Be sure to change password to a strong password of your choosing:

```shell
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '123123';
```

By default MySQL 8 root user can acccess from only localhost. If you want to access it from any host then run:

```shell
UPDATE mysql.user SET host='%' WHERE user='root';
```

Then, run FLUSH PRIVILEGES which tells the server to reload the grant tables and put your new changes into effect:
```shell
FLUSH PRIVILEGES;
```

Set MySQL sever time zone:

```shell
SET GLOBAL time_zone = '+1:00';
```


Once you confirm this on your own server, you can exit the MySQL shell:
<pre>exit</pre>


# Testing MySQL

mysqladmin is a command line administrative client for MySQL. We’ll use it to connect to the server and output some version and status information:

```shell
mysqladmin -u root -p version
```

# Create a database

```shell
CREATE DATABASE dbname;
```

Grant new database privilege to a user

```shell
GRANT ALL ON dbname.* TO 'user'@'localhost';
```

```shell
FLUSH PRIVILEGES;
```

# Create a user and privilege
MySQL 8.0 default authentication plugin is caching_sha2_password rather than mysql_native_password, which is the default method in MySQL 5.7 and prior. But CREATE or ALTER your database user to mysql_native_password with the command shown below:

```shell
CREATE USER 'tankibaj'@'%' IDENTIFIED WITH mysql_native_password BY 'password';
```
Grant all databases privilege to new user

```shell
GRANT ALL PRIVILEGES ON * . * TO 'tankibaj'@'%';
```
Or Grant privilege to selected database

```shell
GRANT ALL ON dbname.* TO 'tankibaj'@'%';
```

```shell
FLUSH PRIVILEGES;
```

# Drop user

```shell
drop user tankibaj;
```


# Change port

Open mysql config

```shell
sudo nano /etc/mysql/mysql.conf.d/mysqld.cnf
```

Add following lines

```text
[mysqld]
pid-file        = /var/run/mysqld/mysqld.pid
socket          = /var/run/mysqld/mysqld.sock
datadir         = /var/lib/mysql
log-error       = /var/log/mysql/error.log
bind-address    = 0.0.0.0
port            = 6969
```

Restart mysql

```shell
service mysql restart
```

