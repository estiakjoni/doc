### Install NFS Kernel Server

```bash
sudo apt update && sudo apt install nfs-kernel-server
```



### NFS Share



##### Private

Create directory for share

```bash
sudo mkdir -p /home/naim/nfs_share
```

Provide  the read, write and execute privileges to all the contents inside the directory.

```bash
sudo chmod 777 /home/naim/nfs_share
```

NFS exports config

```bash
sudo nano /etc/exports
```

Paste following

```
/home/naim/nfs_share 192.168.43.0/24(rw,sync,no_subtree_check)
```

Explanation about the options used in the above command.

- **home/naim/nfs_share**: sharing folder path.
- **192.168.43.0/24**: allowed en entire subnet to have access to the NFS share.
- **rw**: Stands for Read/Write.
- **sync**: Requires changes to be written to the disk before they are applied.
- **No_subtree_check**: Eliminates subtree checking.

Then apply the new settings by running the following command. This will export all directories listed in /etc/exports file.

```bash
sudo exportfs -arvf
```



##### Public

Create directory for share

```bash
sudo mkdir -p /home/naim/nfs_share
```

Provide  the read and execute privileges to all the contents inside the directory.

```bash
sudo chmod 755 /home/naim/nfs_share
```

NFS exports config

```bash
sudo nano /etc/exports
```

Paste following

```
/home/naim/nfs_share *(ro,no_subtree_check,insecure)
```

Explanation about the options used in the above command.

- **home/naim/nfs_share**: sharing folder path.
- *****: allowed any network and IP to have access to the NFS share.
- **ro**: Stands for Read Only.
- **No_subtree_check**: Eliminates subtree checking.
- **insecure**: Stands for no password check.

Then apply the new settings by running the following command. This will export all directories listed in /etc/exports file.

```bash
sudo exportfs -arvf
```



### Enable NFS Server

```bash
sudo systemctl start nfs-kernel-server
sudo systemctl enable nfs-kernel-server
sudo systemctl status nfs-kernel-server
```



### Allow firewall

```bash
sudo ufw allow from 192.168.43.0/24 to any port nfs
OR
sudo ufw allow nfs
```



### Mount NFS on Client
Install the NFS-Common Package

```bash
sudo apt update && sudo apt install nfs-common
```

Mount

```bash
sudo mount 192.168.43.100:/home/naim/nfs_share /mnt/nfs
OR
sudo mount -t nfs -o resvport 192.168.43.100:/home/naim/nfs_share /mnt/nfs
```

