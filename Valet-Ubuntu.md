## Valet Ubuntu
It was initially available only for iOS, but today with the help of a third party package, we can install Valet on Ubuntu.

Also, make sure that your system isn’t running any programs that bind port 80 (like Apache or Nginx).

## Dependencies and packages:

```bash
sudo apt-get install libnss3-tools jq xsel zip unzip curl git
```

## PHP and PHP Packages

###### (Optional For PHP v7.3) Run the commands below to add third-party repository to upgrade to PHP 7.3

```shell
sudo apt-get install software-properties-common
sudo add-apt-repository ppa:ondrej/php
sudo apt update && sudo apt upgrade
```

###### Install PHP

```bash
sudo apt -y install php* php*-cgi php*-common php*-mbstring php*-xml php*-gd php*-opcache php*-fpm php*-zip php*-curl php*-json php*-bcmath php*-readline php*-cli
```

## Installation Composer
<ul>
<li> Command-line installation

```bash
php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
php -r "if (hash_file('sha384', 'composer-setup.php') === 'a5c698ffe4b8e849a443b120cd5ba38043260d5c4023dbf93e1558871f1f07f58274fc6f4c93bcfd858c6bd0775cd8d1') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
php composer-setup.php
php -r "unlink('composer-setup.php');"
```
</li>


<li>Composer Executable Globally. You can place the Composer PHAR anywhere you wish. If you put it in a directory that is part of your PATH, you can access it globally. On Unix systems you can even make it executable and invoke it without directly using the php interpreter.

```bash
sudo mv composer.phar /usr/local/bin/composer
```
</li>

<li>Make laravel globally

[Click here](https://github.com/tankibaj/docs/blob/master/composer-path-global.md)
</li>

</ul>


## Valet installation
<ul>
<li>Finally, require the package valet

```bash
composer global require cpriego/valet-linux
```
</li>


<li>Install valet

```bash
valet install
```
</li>


<li>Once Valet is installed, try pinging any <code>*.test</code> domain on your terminal using a command such as <code>ping foobar.test</code>. If Valet is installed correctly you should see this domain responding on  127.0.0.1.
</li>


<li>Create a new directory on your pc by running something like <code>mkdir ~/sites</code>. Next,  <code>cd ~/sites</code> and run <code>valet park</code>. This command will register your current working directory as a path that Valet should search for sites.</li>

<li>Next, create a new Laravel site within this directory: <code>laravel new blog</code>.</li>

<li>Open http://blog.test in your browser.</li>

</ul>
