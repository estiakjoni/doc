## How to Install Latest Node.js and NPM on Ubuntu with PPA
Node.js is a platform built on Chrome’s JavaScript runtime for easily building fast, scalable network applications. Latest version node.js ppa is maintaining by its official website. We can add this PPA to your Ubuntu 19.04, 18.04 LTS, 16.04 LTS (Trusty Tahr) and 14.04 LTS (Xenial Xerus) systems and install node.js on Linux VPS with easy commands.

#### Step 1 – Add Node.js PPA
Node.js package is available in LTS release and the current release. It’s your choice to select which version you want to install on the system as per your requirements. Let’s add the PPA to your system to install Nodejs on Ubuntu.

###### Use Current Release: At te last update of this tutorial, Node.js 12 is the current Node.js release available.

```shell
sudo apt-get install curl
curl -sL https://deb.nodesource.com/setup_15.x | sudo -E bash -
```

###### Use LTS Release : At the last update of this tutorial, Node.js 10.16.3 is the LTS release available.

```shell
sudo apt-get install curl
curl -sL https://deb.nodesource.com/setup_14.x | sudo -E bash -
```

#### Step 2 – Install Node.js on Ubuntu
You can successfully add Node.js PPA to Ubuntu system. Now execute the below command install Node on and Ubuntu using apt-get. This will also install NPM with node.js. This command also installs many other dependent packages on your system.

```shell
sudo apt-get install nodejs
```

#### Step 3 – Check Node.js and NPM Version

Check the installed version.

```shell
node -v
```

Check the npm version

```shell
npm -v
```
