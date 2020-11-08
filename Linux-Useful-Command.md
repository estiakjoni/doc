## Linux Useful Command

#### Find Out IP Address

```bash
ip a | grep inet
```

OR

```bash
ifconfig | grep inet
```



#### Find Out Default Gateway

```bash
route -n
```

OR

```bash
ip route show
```

OR

```bash
ip r
```



#### Find Out DNS | Nanme Server

```bash
systemd-resolve --status | grep DNS
```

OR

```bash
cat /run/systemd/resolve/resolv.conf
```

OR

```bash
systemd-resolve --status
```



#### Find Out Public IPv6

```bash
curl -s ipv6.icanhazip.com | xargs echo -n
```



#### Directories listing with their total sizes

```bash
du -sh *
```

short version of:

```bash
du --summarize --human-readable *
```

