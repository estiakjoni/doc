# How to Install Composer on MacOS



The PHP Composer is a package management tool. It removes the hassle of maintaining PHP packages for an application manually. You can easily install all the required packages using Composer. It maintains a list of required packages in a JSON file called composer.json. This tutorial helps you to install and configure PHP composer on macOS operating system.



## 1. Prerequisites

- Shell access to a running macOS.
- PHP 5.3 or higher version must be installed.



## 2. Install Composer on macOS

Download composer binary file from getcomposer.org website by running the following command. It will create a composer.phar file in the current directory.

```bash
curl -sS https://getcomposer.org/installer | php
```



Now, copy this composer.phar file under bin directory to make available anywhere in the system. Also, set the execute permission on file. I have changed the filename from composer.phar to composer for the easy use.

```bash
mv composer.phar /usr/local/bin/composer
chmod +x /usr/local/bin/composer
```



Run composer command on the command prompt. This will provide you composer version details along with options available with composer command.

```bash
composer -V
#Composer version 1.9.3 2020-02-04 12:58:49
```
