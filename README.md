# Secu-reseau

- Open the VMS, add network to client1 client2 in order to have ssh, puTTY

## 1.Personnaliser VM

### Change host name 

- Dir for changing hostname: `nano /etc/hostname`
- Apply mod: `systemctl restart hostname.service`

### Config IP

- Dir for changing ip config `nano /etc/network/interfaces`
  - Box config:
``` 
# This file describes the network interfaces available on your system
# and how to activate them. For more information, see interfaces(5).

source /etc/network/interfaces.d/*

# The loopback network interface
auto lo
iface lo inet loopback

# The primary network interface
allow-hotplug ens33
allow-hotplug ens35
allow-hotplug ens36

iface ens33 inet dhcp

iface ens35 inet static
        address 10.0.1.1
        netmask 255.255.255.0

iface ens36 inet static
        address 10.0.2.1
        netmask 255.255.255.0

```
  - client1 config
```
allow-hotplug ens33
iface ens33 inet static
        address 10.0.1.2
        netmask 255.255.255.0
```
  - client 2 config
```
allow-hotplug ens33
iface ens33 inet static
        address 10.0.2.2
        netmask 255.255.255.0
```

- Apply changes: `systemctl restart networking.service`
- Faire une ping pour verifier
### IP Fowarding Box
- Sur le box on modifie: `nano /etc/sysctl.conf`
- Uncomment the line: `net.ipv4.ip_forward=1`
- Apply changes: `sysctl -p`
- Variable: Attention 
### NFTables
`nft add table ip nat`
`nft add chain ip nat postrouting { type nat hook postrouting priority 100 \; }`
`nft add rule ip nat postrouting oifname "eth0" masquerade`
`nft list ruleset > /etc/nftables.conf`
`systemctl enable nftables`
The conf file will be
```
table ip nat {
        chain postrouting {
                type nat hook postrouting priority srcnat; policy accept;
                oifname "ens33" masquerade
        }
}
```

## DNS
`apt install bind9 bind9utils bind9-doc`

`nano /etc/resolv.conf `

`nano /etc/bind/named.conf.local`

