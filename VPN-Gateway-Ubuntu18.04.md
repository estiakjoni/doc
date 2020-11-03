## IP Addressing

My network is setup as follows:

My home network is setup as follows:

- Internet Router: `192.168.0.1`
- Subnet Mask: `255.255.255.0`
- Router gives out DHCP range: `192.168.0.100-200`
- ubuntuBox Ethernet Interface name: `enp0s3`

If your network range is different, that's fine, use your network range instead of mine. I'm going to give my ubuntuBox a static IP address of `192.168.1.2`

## NTP

 Accurate time is important for the VPN encryption to work. If the VPN client's clock is too far off, the VPN server will reject the client.You shouldn't have to do anything to set this up, the `ntp` service is installed and enabled by default.

Double-check your ubuntuBox is getting the correct time from internet time servers with `ntpq -p`.



## Setup VPN Client

Install the OpenVPN client:

```bash
sudo apt-get install openvpn
```

Get OpenVPN profiles/certificates from your vpn provider and copy them to OpenVPN client  

`/etc/openvpn/` path. Note the extension change from **ovpn** to **conf**.

```bash
sudo cp openvpn/Germany.ovpn /etc/openvpn/Germany.conf
```

Create `sudo nano /etc/openvpn/login` containing only your username and password, one per line, for example:

```
User
Password
```

Change the permissions on this file so only the root user can read it:

```bash
sudo chmod 600 /etc/openvpn/login
```

Edit OpenVPN profiles/certificates `sudo nano /etc/openvpn/Germany.conf` to use your stored username and password. Change the line that says, **auth-user-pass** to **auth-user-pass /etc/openvpn/login**

```
auth-user-pass /etc/openvpn/login
```



## Test VPN

At this point you should be able to test the VPN actually works:

```bash
sudo openvpn --config /etc/openvpn/Germany.conf
```



## Enable VPN at boot

```bash
sudo systemctl enable openvpn@Germany
```



## Setup Routing and NAT

Enable IP Forwarding:

```bash
echo -e '\n#Enable IP Routing\nnet.ipv4.ip_forward = 1' | sudo tee -a /etc/sysctl.conf
sudo sysctl -p
```

Setup NAT from the local LAN down the VPN tunnel:

```bash
sudo iptables -t nat -A POSTROUTING -o tun0 -j MASQUERADE
sudo iptables -A FORWARD -i tun0 -o enp0s3 -m state --state RELATED,ESTABLISHED -j ACCEPT
sudo iptables -A FORWARD -i enp0s3 -o tun0 -j ACCEPT
```

Make the NAT rules persistent across reboot:

```bash
sudo apt-get install iptables-persistent
```

The installer will ask if you want to save current rules, select **Yes**. If you don't select yes, that's fine, you can save the rules later with `sudo netfilter-persistent save` Make the rules apply at startup:

```bash
sudo systemctl enable netfilter-persistent
```



## VPN Kill Switch

This will block outbound traffic from the ubuntuBox so that only the VPN and related services are allowed.

Once this is done, the only way the ubuntuBox can get to the internet is over the VPN.

This means if the VPN goes down, your traffic will just stop working, rather than end up routing over your regular internet connection where it could become visible.

```bash
sudo iptables -A OUTPUT -o tun0 -m comment --comment "vpn" -j ACCEPT
sudo iptables -A OUTPUT -o enp0s3 -p icmp -m comment --comment "icmp" -j ACCEPT
sudo iptables -A OUTPUT -d 192.168.0.0/24 -o enp0s3 -m comment --comment "lan" -j ACCEPT
sudo iptables -A OUTPUT -o enp0s3 -p udp -m udp --dport 1198 -m comment --comment "openvpn" -j ACCEPT
sudo iptables -A OUTPUT -o enp0s3 -p tcp -m tcp --sport 22 -m comment --comment "ssh" -j ACCEPT
sudo iptables -A OUTPUT -o enp0s3 -p udp -m udp --dport 123 -m comment --comment "ntp" -j ACCEPT
sudo iptables -A OUTPUT -o enp0s3 -p udp -m udp --dport 53 -m comment --comment "dns" -j ACCEPT
sudo iptables -A OUTPUT -o enp0s3 -p tcp -m tcp --dport 53 -m comment --comment "dns" -j ACCEPT
sudo iptables -A OUTPUT -o enp0s3 -j DROP
```