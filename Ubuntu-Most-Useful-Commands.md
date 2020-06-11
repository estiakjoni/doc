# Ubuntu Most Useful Commands

### Check size of directory

```bah
sudo du -hs /home/* | sort -rh | head -10
```



### Check disk information

```bash
sudo df -h
```



### View nginx logs

```bash
sudo tail -n 100 /var/log/nginx/access.log
```



### Failed SSH log-in attempt logs

```bash
grep sshd.\*Failed /var/log/auth.log | less
```

Or

```bash
journalctl _SYSTEMD_UNIT=ssh.service | egrep "Failed|Failure"
```

Or

```bash
egrep "Failed|Failure" /var/log/auth.log
```

Or

```bash
cat /var/log/auth.log | grep "Failed password"
```

