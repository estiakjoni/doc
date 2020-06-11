# rclone



### Mount

```bash
sudo rclone mount ovh:naim/ /var/www/ovh --daemon --allow-other
```

with buffer

```bash
sudo rclone mount ovh:naim/ /var/www/ovh --daemon --allow-other --buffer-size 150M --max-read-ahead 100M --dir-cache-time 150m0s
```





### Unmount

```bash
sudo fusermount -u /var/www/ovh
```



### Check mounted disk

```bash
df -h
```



### List

List all the objects in the path with size, modification time and path.

```bash
lsl ovh:naim/movies/
```

List all directories/containers/buckets in the path.

```
rclone lsd remote:path
```

List all the objects in the path with size and path.

```
rclone ls BackupStorage:naim/movie/
```



### Copy file

```bash
rclone copy /local/path ovh:naim
```



### Docs

https://rclone.org/docs/