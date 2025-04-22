## rpmbuilds
RPM builds repository

### MSTPD - rapid and multi Spanning Tree support for linux - userspace driver

- Source code link:

[MSTPD source](https://github.com/mstpd/mstpd.git)

- This repository contains mstpd builds for the following RPM-based distributions:

	1. Almalinux 9
	2. Rocky linux 9
	3. Oracle linux 9
	4. Mageia 8
	5. Mageia 9
	
#### Usage:

- Git clone the repository

```
git clone https://github.com/mariusavram123/rpmbuilds
```

- Install the rpm packages

```
cd RPMS/<your_distro>
dnf install ./mstpd-0.1.0-1.<your_distro>.x86_64.rpm
```

- Start and enable mstpd daemon with systemctl

```
systemctl start mstpd
systemctl enable mstpd
```

- Use Rapid Spanning Tree Protocol for a bridge in linux

```
mstpctl setforcevers bridge0 rstp
```

- Show the ports status on the bridges

```
mstpctl showbridge
```

- More documentation:

[Documentation-mstpd](https://software-dl.ti.com/processor-sdk-linux/esd/docs/05_01_00_11/linux/Industrial_Protocols_RSTP.html)

### BIRD - Bird Internet routing daemon for Mageia 8 and Mageia 9

- Getting the source RPM:

	- On an Almalinux 9 VM use the command:
	```
	dnf download --source bird
	```
	- Transfer it to Mageia 8 or Mageia 9 build system 
	
	- Build the RPM
	
- Installing bird on Mageia 9 (run the commands as root):

```
git clone https://github.com/mariusavram123/rpmbuilds
cd RPMS/mga9/
dnf install ./bird-2.15.1-2.mga9.x86_64.rpm
systemctl start bird
systemctl enable bird
```

- For Mageia 8 use the following commands:

```
git clone https://github.com/mariusavram123/rpmbuilds
cd RPMS/mga8/
dnf install ./bird-2.15.1-2.mga8.x86_64.rpm
systemctl start bird
systemctl enable bird
```

### Configure bird for a BGP connection - make sure it advertises routes to neighbor

- Edit /etc/bird.conf. List any prefixes which you want to advertise in static protocol field

```
router id 10.85.0.1;

protocol static {
        ipv4;                   # Again, IPv4 channel with default options
        route 10.85.0.0/30 via 10.85.0.1;
        route 10.101.101.101/32 via 10.85.0.1;
}


 protocol bgp testmarius {
       description "My BGP test";
       local 10.85.0.1 as 65001;
       neighbor 10.85.0.2 as 65000;
       hold time 180;           # Default is 240
#       password "secret";      # Password used for MD5 authentication

       ipv4 {                  # regular IPv4 unicast (1/1)
               #table main;
               import all;
               #import filter rt_import;
               #export all;
               #export filter export_subnets;
               export where source ~ [ RTS_STATIC, RTS_BGP ];
               graceful restart on;
               #export where source ~ [ 10.85.0.0/30, 10.101.101.101/32 ];
       };

#       ipv6 {                  # regular IPv6 unicast (2/1)
#               import filter rt_import;
#               export filter { # The same as 'where' expression above
#                       if source ~ [ RTS_STATIC, RTS_BGP ]
#                       then accept;
#                       else reject;
#               };
#       };
#
#       ipv4 multicast {        # IPv4 multicast topology (1/2)
#               table mrib4;    # explicit IPv4 table
#               import filter rt_import;
#               export all;
#       };
#
#       ipv6 multicast {        # IPv6 multicast topology (2/2)
#               table mrib6;    # explicit IPv6 table
#               import filter rt_import;
#               export all;
#       };
}
```

- Save the file and restart bird.service `systemctl restart bird`

- View the routes in the kernel:

- Mageia 8

```
[root@test-bird-bgp-mga8 ~]# ip route
(...)
10.85.0.0/30 via 10.85.0.1 dev ens9 proto bird metric 32 
10.85.0.0/30 dev ens9 proto kernel scope link src 10.85.0.1 metric 101 
10.101.101.101 via 10.85.0.1 dev ens9 proto bird metric 32 
10.102.102.102 via 10.85.0.2 dev ens9 proto bird metric 32 
(...)
```

- Almalinux 9 - using FRR:

```
[root@alma9-testbgp ~]# ip route
(...)
10.85.0.0/30 dev enp7s0 proto kernel scope link src 10.85.0.2 metric 101 
10.101.101.101 nhid 17 via 10.85.0.1 dev enp7s0 proto bgp metric 20 
172.16.29.0/24 dev enp1s0 proto kernel scope link src 172.16.29.12 metric 100 
(...)
```

- PS: By default routes for dummy interfaces does not appear in kernel for interfaces managed by NMCLI

- FRR configuration Almalinux 9

```ios
alma9-testbgp# show running-config 
Building configuration...

Current configuration:
!
frr version 10.3
frr defaults traditional
hostname alma9-testbgp
no ipv6 forwarding
!
router bgp 65000
 no bgp ebgp-requires-policy
 no bgp network import-check
 neighbor 10.85.0.1 remote-as 65001
 !
 address-family ipv4 unicast
  network 10.85.0.0/30
  network 10.102.102.102/32
  neighbor 10.85.0.1 soft-reconfiguration inbound
 exit-address-family
exit
!
segment-routing
 traffic-eng
 exit
exit
!
end
```
