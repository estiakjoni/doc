## Installing the Yaru community theme in Ubuntu 18.04

#### Install Communitheme
To install the Communitheme Snap package, use the command below:

```shell
sudo snap install communitheme
```

#### PPA
The [official Communitheme PPA](https://launchpad.net/~communitheme/+archive/ubuntu/ppa) is only available for Ubuntu 18.04. You CANNOT use this PPA in 16.04 or 17.10.

Open a terminal and use the following commands:

```shell
sudo add-apt-repository ppa:communitheme/ppa
sudo apt update
sudo apt install ubuntu-communitheme-session
```

#### Install Unity Tweak tool

```shell
sudo apt-get install unity-tweak-tool
```

## Change Theme
Once you have the Communitheme session installed, you can change the theme and icon using GNOME Tweaks. You’ll have to change the Applications theme to Communitheme and icons to Suru

![img](https://i.imgur.com/lEpmhNd.jpg)
