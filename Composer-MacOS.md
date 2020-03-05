# How to Install Composer on MacOS



The PHP Composer is a package management tool. It removes the hassle of maintaining PHP packages for an application manually. You can easily install all the required packages using Composer. It maintains a list of required packages in a JSON file called composer.json. This tutorial helps you to install and configure PHP composer on macOS operating system.



## 1. Prerequisites

- Shell access to a running macOS.
- PHP 5.3 or higher version must be installed.



## 2. Install Composer on macOS

Download composer.phar binary file from getcomposer.org website. Next, copy this composer.phar file under bin directory to make available anywhere in the system. Also, set the execute permission on file. I have changed the filename from composer.phar to composer for the easy use.

```bash
curl -sS https://getcomposer.org/installer | php && \
mv composer.phar /usr/local/bin/composer && \
chmod +x /usr/local/bin/composer
```



Run composer command on the command prompt. This will provide you composer version details along with options available with composer command.

```bash
composer -V
#Composer version 1.9.3 2020-02-04 12:58:49
```



Add the `~/.composer/vendor/bin` directory in system's "PATH"

If your PATH is in .zshrc file :

```bash
echo 'export PATH="$HOME/.composer/vendor/bin:$PATH"' >> ~/.zshrc && source ~/.zshrc
```

If your PATH is in .bashrc file :

```bash
echo 'export PATH="$HOME/.composer/vendor/bin:$PATH"' >> ~/.bashrc && source ~/.bashrc
```

