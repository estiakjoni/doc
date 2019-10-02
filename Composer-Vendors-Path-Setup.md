# Download Composer
To quickly install Composer in the current directory, run the following script in your terminal. 

```shell
php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
php -r "if (hash_file('sha384', 'composer-setup.php') === 'a5c698ffe4b8e849a443b120cd5ba38043260d5c4023dbf93e1558871f1f07f58274fc6f4c93bcfd858c6bd0775cd8d1') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
php composer-setup.php
php -r "unlink('composer-setup.php');"
```

Official download link  [https://getcomposer.org/download/](https://getcomposer.org/download/)

# Globally Composer

You can place the Composer PHAR anywhere you wish. If you put it in a directory that is part of your PATH, you can access it globally. On Unix systems you can even make it executable and invoke it without directly using the php interpreter.

After downloaded, you can run this to move composer.phar to a directory that is in your path:

```shell
mv composer.phar /usr/local/bin/composer
```


# Setting path for composer vendors

**For Mac**
```shell
echo 'export PATH="$HOME/.composer/vendor/bin:$PATH"' > ~/.bashrc
source ~/.bashrc
```

**For Ubuntu 18.04**

```shell
echo 'export PATH="~/.config/composer/vendor/bin:$PATH"' > ~/.bashrc
source ~/.bashrc
```
