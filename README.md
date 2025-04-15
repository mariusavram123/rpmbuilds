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
