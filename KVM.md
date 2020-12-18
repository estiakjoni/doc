# KMV

**KVM** (Kernel-based Virtual Machine) is an open source full virtualization solution for Linux like systems, KVM provides virtualization functionality using the virtualization extensions like **Intel VT** or **AMD-V**.



## Verify whether your system support hardware virtualization

```bash
egrep -c '(vmx|svm)' /proc/cpuinfo
```

If the output is greater than 0 then it means your system supports Virtualization else reboot your system, then go to BIOS settings and enable VT technology.



## Install KVM required packages

```bash
sudo apt install -y qemu qemu-kvm libvirt-daemon libvirt-clients bridge-utils virt-manager
```

A little explanation of the above packages.

- The **qemu** package (quick emulator) is an application that allows you to perform hardware virtualization.
- The **qemu-kvm** package is the main KVM package.
- The **libvritd-daemon** is the virtualization daemon.
- The **bridge-utils** package helps you create a bridge connection to allow other users to access a virtual machine other than the host system.
- The **virt-manager** is an application for managing virtual machines through a graphical user interface.

Before proceeding further, we need to confirm that the virtualization daemon – **libvritd-daemon** – is running. To do so, execute the command.

```bash
sudo systemctl status libvirtd
```

You can enable it to start on boot by running:

```bash
sudo systemctl enable --now libvirtd
```

To check if the KVM modules are loaded, run the command:

```bash
lsmod | grep -i kvm
```

#### User & Groups

```bash
sudo adduser $USER libvirt && \
sudo adduser $USER kvm
```



## Network Bridge for KVM virtual Machines

Network bridge is required to access the KVM based virtual machines outside the KVM hypervisor or host. In Ubuntu 18.04, network is managed by netplan utility, whenever we freshly installed Ubuntu 18.04 server then netplan file is created under **/etc/netplan/.**

```bash
ls /etc/netplan/
cat /etc/netplan/*.yaml
sudo nano /etc/netplan/*.yaml
```

As of now I have already configured the static IP via this file and content of this file is below:

```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
        enp2s0:
            addresses: [192.168.0.100/24]
            gateway4: 192.168.0.1
            nameservers:
              addresses: [192.168.0.1]
            dhcp4: no
            optional: true
    version: 2
```

Let’s add the network bridge definition in this file,

```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    enp2s0:
      dhcp4: no
      dhcp6: no

  bridges:
    br0:
      interfaces: [enp2s0]
      dhcp4: no
      addresses: [192.168.0.100/24]
      gateway4: 192.168.0.1
      nameservers:
        addresses: [8.8.8.8,1.1.1.1]
```

As you can see we have removed the IP address from interface(ens33) and add the same IP to the bridge ‘**br0**‘ and also added interface (ens33) to the bridge br0. Apply these changes using below netplan command:

```bash
sudo netplan try
sudo netplan --debug  apply
sudo netplan apply
sudo networkctl status -a
```

Check out IP

```bash
ip a
```



## Create a guest instance

Before creating the guest VM, we need to ensure the Guest OS is supported by the KVM, can check  by running the following command :

```bash
sudo apt install -y libosinfo-bin
```

```bash
osinfo-query os
```

**virt-install usage:**

|                          Arguments                           |                           Remarks                            |
| :----------------------------------------------------------: | :----------------------------------------------------------: |
|                        --name VM_NAME                        |                  Name of the guest instance                  |
|                        --memory 1024                         |           Configure guest memory allocation in MiB           |
|            --memory memory=1024,currentMemory=512            | Configure guest memory allocation maximum and current in MiB |
|                          --vcpus 1                           |         Number of vcpus to configure for your guest          |
|                    --vcpus 5,maxvcpus=10                     | Current and maximum number of vcpus to configure for your guest |
|                   --os-variant ubuntu18.04                   |             The OS being installed in the guest              |
|                        --disk size=10                        |     Specify storage with 10GiB image in default location     |
| --disk path=/mtn/kvm/ubuntu.qcow2,format=qcow2,bus=virtio,size=10 |        Specify storage with location, format and size        |
|                     --network bridge=br0                     |             Configure a guest network interface              |
|                       --graphics none                        |               Configure guest display settings               |
|                --cdrom /home/user/ubuntu.iso                 |                  CD-ROM installation media                   |
|       --extra-args="console=tty0 console=ttyS0,115200"       | Additional arguments to pass to the install kernel booted from --location |
|                       --check all=off                        |             Enable or disable validation checks              |
|                            --hvm                             | [Optional] This guest should be a fully virtualized guest. Create a fully-virtualized Windows guest using the command-line (`virt-install`), launch the operating system's installer inside the guest, and access the installer through `virt-viewer`. |



```
export ISO="/home/naim/Downloads/ubuntu-18.04.5-live-server-amd64.iso"
export NAME="VPN-Gateway"

sudo virt-install \
--virt-type kvm \
--name ${NAME} \
--memory 1024 \
--vcpus 1 \
--hvm \
--cdrom ${ISO} \
--network bridge:br0 \
--disk size=10
```



## Virsh KVM management

Let us see some useful commands for managing VMs.

#### List all VMs

```bash
virsh list --all
```

#### Get VM info

```bash
virsh dominfo vmname
```

#### Stop/shutdown a VM

```bash
virsh shutdown vmname
```

#### Start VM

```bash
virsh start vmname
```

#### Mark VM for autostart at server boot time

```bash
virsh autostart vmname
```

#### Reboot (soft & safe reboot) VM

```bash
virsh reboot vmname
```

#### Reset (hard reset/not safe) VM

```bash
virsh reset vmname
```

#### Delete VM

```bash
virsh shutdown vmname
virsh undefine vmname
virsh pool-destroy vmname
D=/var/lib/libvirt/images
VM=centos8-vm1.img 
sudo rm -ri $D/$VM
```

#### List the current snapshots

```bash
virsh snapshot-list vmname
```

#### Create a Snapshot

```bash
virsh snapshot-create-as --domain vmname --name "snapshot_name" --description "my description"
virsh snapshot-list vmname
```

#### To check the details of a snapshot

```bash
virsh snapshot-list vmname
virsh snapshot-info --domain vmname --current
```

#### To revert to a snapshot [snapshot restore]

```bash
virsh shutdown vmname
virsh snapshot-revert --domain vmname --snapshotname "snapshot_name" --running
```

#### To delete a snapshot

```bash
virsh snapshot-delete --domain vmname --snapshotname "snapshot_name"
```



## Storage Pool

As a default, there is one storage pool which called “**Default**” uses the **rootfs** partition to store vm’s volumes under **/var/lib/libvirt/images** path.

```bash
virsh pool-list
```

I want to use the directory `/mnt/kvm/` which is mounted over `/dev/sda5`, as the default Storage Pool for all future situations. I will use `/dev/sda5` as my partition, you may have a different one. Make sure you have mounted it properly.

```bash
sudo mount -t ext4 /dev/sda5 /mnt/kvm/
```

**Listing current pools:**

```bash
virsh pool-list
```

**Destroying pool:**

```bash
virsh pool-destroy default
```

**Undefine pool:**

```bash
virsh pool-undefine default
```

**Creating a directory to host the new pool (if it does not exist):**

```
sudo mkdir /mnt/kvm/
```

**Defining a new pool with name "default":**

```bash
virsh pool-define-as --name default --type dir --target /mnt/kvm/
```

**Set pool to be started when libvirt daemons starts:**

```bash
virsh pool-autostart default
```

**Start pool:**

```bash
virsh pool-start default
```

**Checking pool state:**

```bash
virsh pool-list
```

