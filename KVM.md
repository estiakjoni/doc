# KMV

**KVM** (Kernel-based Virtual Machine) is an open source full virtualization solution for Linux like systems, KVM provides virtualization functionality using the virtualization extensions like **Intel VT** or **AMD-V**.



## Verify Whether your system support hardware virtualization

```bash
egrep -c '(vmx|svm)' /proc/cpuinfo
```

If the output is greater than 0 then it means your system supports Virtualization else reboot your system, then go to BIOS settings and enable VT technology.



## Install KVM and its required packages

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



## Configure Network Bridge for KVM virtual Machines

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



## Create a virtual machine

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



## virsh KVM management

Let us see some useful commands for managing VMs.

#### List all VMs

```bash
sudo virsh list --all
```

#### Get VM info

```bash
sudo virsh dominfo vmname
```

#### Stop/shutdown a VM

```bash
sudo virsh shutdown vmname
```

#### Start VM

```bash
sudo virsh start vmname
```

#### Mark VM for autostart at CentOS 8 server boot time

```bash
sudo virsh autostart vmname
```

#### Reboot (soft & safe reboot) VM

```bash
sudo virsh reboot vmname
```

#### Reset (hard reset/not safe) VM [last resort]

```bash
sudo virsh reset vmname
```

#### Delete VM

```bash
sudo virsh shutdown vmname
sudo virsh undefine vmname
sudo virsh pool-destroy vmname
D=/var/lib/libvirt/images
VM=centos8-vm1.img 
sudo rm -ri $D/$VM
```

#### List the current snapshots

```bash
sudo virsh snapshot-list vmname
```

#### Create a Snapshot

```bash
sudo virsh snapshot-create-as --domain vmname --name "snapshot_name" --description "my description"
sudo virsh snapshot-list vmname
```

#### To check the details of a snapshot

```bash
sudo virsh snapshot-list vmname
sudo virsh snapshot-info --domain vmname --current
```

#### To revert to a snapshot [snapshot restore]

```bash
sudo virsh shutdown vmname
sudo virsh snapshot-revert --domain vmname --snapshotname "snapshot_name" --running
```

#### To delete a snapshot

```bash
sudo virsh snapshot-delete --domain vmname --snapshotname "snapshot_name"
```

