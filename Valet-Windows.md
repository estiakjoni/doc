## Valet Windows
It was initially available only for iOS, but today with the help of a third party package, we can install Valet on Windows.



## Install PHP
If your system doesn’t have PHP, make sure to [install the latest version of PHP](http://windows.php.net/download) before installing Valet.

## Installation Composer
You must have installed [Composer](https://getcomposer.org/download) to install Valet.


## Valet installation
<ul>
<li>Finally, require the package valet. Also, make sure that your system isn’t running any programs that bind port 80 (like Apache or Nginx). And run the commands shown below as Administrator.

```bash
composer global require cretueusebiu/valet-windows
```
</li>


<li>Install valet

```bash
valet install
```
</li>


<li>We need to configure <code>127.0.0.1</code> as IPv4 Preferred DNS server and <code>::1</code> as IPv6 Preferred DNS server.

![Imgur](https://i.imgur.com/xdiwo9a.png)
</li>







<li>Once Valet is installed, try pinging any <code>*.test</code> domain on your terminal using a command such as <code>ping foobar.test</code>. If Valet is installed correctly you should see this domain responding on  127.0.0.1.
</li>


<li>Create a new directory on your pc by running something like <code>mkdir ~/sites</code>. Next,  <code>cd ~/sites</code> and run <code>valet park</code>. This command will register your current working directory as a path that Valet should search for sites.</li>

<li>Next, create a new Laravel site within this directory: <code>laravel new blog</code>.</li>

<li>Open http://blog.test in your browser.</li>

</ul>
