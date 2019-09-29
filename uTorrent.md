## Download uTorrent:

```shell
wget http://download-hr.utorrent.com/track/beta/endpoint/utserver/os/linux-x64-debian-7-0/utserver.tar.gz
```

## Install dependencies:

```shell
apt install libssl1.0.0 libssl-dev
```

## Download dependencies: [Optional] [docs](https://packages.debian.org/jessie/libssl1.0.0)

```shell
wget http://security.debian.org/debian-security/pool/updates/main/o/openssl/libssl1.0.0_1.0.1t-1+deb8u11_amd64.deb
```

**Install**

```shell
dpkg --install libssl1.0.0_1.0.1t-1+deb8u11_amd64.deb
```

## Unzip, Setup & Run:

```shell
tar -zxvf utserver.tar.gz -C /opt/
```

```shell
chmod 777 /opt/utorrent-server-alpha-v3_3/
```

```shell
ln -s /opt/utorrent-server-alpha-v3_3/utserver /usr/bin/utserver
```

```shell
utserver -settingspath /opt/utorrent-server-alpha-v3_3/ &
```

## End Point

```text
http://host:8080/gui
user: admin
password: leave blank
```
