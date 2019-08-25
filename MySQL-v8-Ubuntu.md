# Adding the MySQL Software Repository
Now we’re going to download the file. On your server, move to a directory you can write to:

```shell
cd /tmp
```

Download the file using curl, remembering to paste the address you just copied in place of the highlighted portion below:

```shell
curl -OL https://repo.mysql.com//mysql-apt-config_0.8.13-1_all.deb
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
systemctl status mysql
```

# Securing MySQL
MySQL comes with a command we can use to perform a few security-related updates on our new install. Let’s run it now:

```shell
mysql_secure_installation
```
This will ask you for the MySQL root password that you set during installation. Type it in and press <code>ENTER</code>.

Now we’ll answer a series of yes or no prompts. Let’s go through them:

First, we are asked about the validate password plugin, a plugin that can automatically enforce certain password strength rules for your MySQL users. Enabling this is a decision you’ll need to make based on your individual security needs. Type <code>y</code> and <code>ENTER</code> to enable it, or just hit <code>ENTER</code> to skip it. If enabled, you will also be prompted to choose a level from 0–2 for how strict the password validation will be. Choose a number and hit <code>ENTER</code> to continue.

Next you’ll be asked if you want to change the root password. Since we just created the password when we installed MySQL, we can safely skip this. Hit ENTER to continue without updating the password.

The rest of the prompts can be answered yes. You will be asked about removing the anonymous MySQL user, disallowing remote root login, removing the test database, and reloading privilege tables to ensure the previous changes take effect properly. These are all a good idea. Type y and hit ENTER for each.

The script will exit after all the prompts are answered. Now our MySQL installation is reasonably secured. Let’s test it again by running a client that connects to the server and returns some information.

Open up the MySQL prompt from your terminal:

```bash
sudo mysql -u root -p
```

Next, check which authentication method each of your MySQL user accounts use with the following command:

<pre>SELECT user,authentication_string,plugin,host FROM mysql.user;</pre>


In this example, you can see that the root user does in fact authenticate using the auth_socket plugin. To configure the root account to authenticate with a password, run the following ALTER USER command. Be sure to change password to a strong password of your choosing:

<pre>ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '123123';</pre>


Then, run FLUSH PRIVILEGES which tells the server to reload the grant tables and put your new changes into effect:
<pre>FLUSH PRIVILEGES;</pre>


Once you confirm this on your own server, you can exit the MySQL shell:
<pre>exit</pre>


# Testing MySQL

mysqladmin is a command line administrative client for MySQL. We’ll use it to connect to the server and output some version and status information:

```shell
mysqladmin -u root -p version
```
