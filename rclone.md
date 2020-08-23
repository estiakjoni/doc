# rclone
### Install

```bash
sudo apt install rclone
```



### OVH Config

Once your OpenStack user account has been created, you can retrieve the config file you need for Rclone from your Control Panel.

To do this, when you are on the OpenStack user page in your Control Panel, click the wrench symbol on the right, then on `Download an Rclone config file`.

![](https://docs.ovh.com/cz/cs/storage/sync-rclone-object-storage/images/download_file.png)

Once the file has been downloaded, run following command to setup

```bash
rclone --config <file_path>
```





**Alternative manual config**

Once the file has been downloaded, copy everything from it and run following command to create `rclone.conf` file and paste.

```bash
sudo nano /home/$USER/.config/rclone/rclone.conf
```

```rclone.conf
[ovh]
type = swift
env_auth = false
auth_version = 3
auth = https://auth.cloud.ovh.net/v3/
endpoint_type = public
tenant_domain = default
tenant = 2069126041052156
domain = default
user = <USER>
key = <PASSWORD>
region = DE
```

you can launch the following command to add your new storage space:

```bash
rclone config
```

You can find more detailed instructions on how to synchronise your object storage and Rclone on the official Rclone website: [Official Rclone documentation](https://rclone.org/swift/).



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
