- [Overview](#overview)
- [Install](#install)
          - [Ubuntu](#ubuntu)
          - [macOS](#macos)
- [Download](#download)
        - [FTP SSL - FTPS](#ftp-ssl---ftps)
        - [SSL FTP - SFTP](#ssl-ftp---sftp)
        - [FTP](#ftp)
        - [SSH SCP not password-protected](#ssh-scp-not-password-protected)
        - [SSH SCP password-protected](#ssh-scp-password-protected)
        - [SMB Server](#smb-server)
        - [HTTP password-protected](#http-password-protected)
        - [HTTP](#http)
        - [HTTPS password-protected](#https-password-protected)
        - [HTTPS](#https)
- [Uploading](#uploading)
          - [FTPS - FTP SSL](#ftps---ftp-ssl)
          - [SFTP - SSL FTP](#sftp---ssl-ftp)
          - [FTP Upload](#ftp-upload)
          - [SSH SCP non password protected](#ssh-scp-non-password-protected)
          - [SSH SCP password protected](#ssh-scp-password-protected)
          - [SMB](#smb)
          - [HTTP PUT](#http-put)
          - [HTTP POST](#http-post)
- [Comment](#comment)

# Overview

**curl** is a tool to transfer data from or to a server, using one of the supported protocols (DICT, FILE, FTP, FTPS, GOPHER, HTTP, HTTPS, IMAP, IMAPS, LDAP, LDAPS, POP3, POP3S, RTMP, RTSP, SCP, SFTP, SMB, SMBS, SMTP, SMTPS, TELNET and TFTP)



# Install



### Ubuntu

```shell
sudo apt install curl
```



### macOS

```bash
brew install curl
```





# Download



### FTP SSL - FTPS

```bash
curl -u user:password --ftp-ssl ftp://download.com/file.zip -o /home/user/file.zip
```



### SSL FTP - SFTP

```bash
curl -u user:password sftp://download.com/file.zip -o /home/user/file.zip
```



### FTP

```bash
curl -u user:password ftp://download.com/file.zip -o /home/user/file.zip
```



### SSH SCP not password-protected

```bash
curl -u username: --key ~/.ssh/id_rsa scp://download.com/~/file.zip -o /home/user/file.zip
```



### SSH SCP password-protected

```bash
curl -u username: --key ~/.ssh/id_rsa --pass private_key_password scp://download.com/~/file.zip -o /home/user/file.zip
```



### SMB Server

```bash
curl -u 'domain\username:passwd' smb://server.download.com/share/file.zip -o /home/user/file.zip
```



### HTTP password-protected

```bash
curl -u name:passwd http://download.com/file.zip -o /home/user/file.zip
```



### HTTP

```bash
curl http://download.com/file.zip -o /home/user/file.zip
```



### HTTPS password-protected

```bash
curl -u name:passwd https://download.com/file.zip -o /home/user/file.zip
```



### HTTPS

```bash
curl https://download.com/file.zip -o /home/user/file.zip
```



# Uploading



### FTPS - FTP SSL 

```bash
curl -T /home/user/file.zip -u user:password --ftp-ssl ftp://upload.com/upload/
```



### SFTP - SSL FTP

```bash
curl -T /home/user/file.zip -u user:password sftp://upload.com/upload/
```



### FTP Upload

```bash
curl -T /home/user/file.zip -u user:password ftp://upload.com/upload/
```



### SSH SCP non password protected

```bash
curl -T /home/user/file.zip -u username: --key ~/.ssh/id_rsa scp://upload.com/~/upload/
```



### SSH SCP password protected

```bash
curl -T /home/user/file.zip -u username: --key ~/.ssh/id_rsa --pass private_key_password scp://upload.com/~/upload
```



### SMB

```bash
curl -T /home/user/file.zip -u 'domain\username:passwd' smb://server.upload.com/share/
```



### HTTP PUT

```bash
curl -T /home/user/file.zip -u http://upload.com/
```

Note: the HTTP server must have been configured to accept PUT before this can be done successfully.



### HTTP POST

```bash
curl -i -X POST -H "Content-Type: multipart/form-data" -F "data=@/home/user1/Desktop/test.jpg" http://upload.com/
```







# Comment

For more information visit following site:

https://curl.haxx.se/docs/manpage.html

https://curl.haxx.se/docs/manual.html