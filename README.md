# OpenBSD vmm(4) 

The idea of this repo is to setup an example vmm(4) environment to run VMs over OpenBSD, with OpenBSD.

For debian on vmm(4) you can also see @vext01:

https://github.com/vext01/recipes/blob/master/recipes/debian9_inside_vmm.md


# WARNING: This will override your files if exists

### hostname.vether0

This file allows configuration of the VM network. The installed default has it's own subnet, 10.10.10.0/24. The DHCP server runs at 10.10.10.254 and is configured via dhcpd.conf.

### hostname.bridge0

This file contains the bridge configuration of your network, allowing VMs talking to vether0 to get a DHCP address and be forwarded to the internet. In the new syntax, ```add vether0``` has been removed and replaced with ```interface bridge0``` so now the network looks like this:

``` vm ---> bridge0 ---> vether0 ---> internet```

### pf.conf

A typical pf.conf, $ext_if are our inet interfaces, and $int_if are our 'internal', in this case, the
internals interfaces are the ones connected to VMs, in this case vether0 and tapX. You need to edit this
according to your machine.

### sysctl.conf

Used for miscellaneous system and kernel settings, sysctl.conf is used to allow traffic forwarding, from and to the external interface and your VM network.

### dhcpd.conf

A quick 'n dirty DHCP server configuration, setting the VM network and nameserver (currently, Quad9 is configured in the default - personally I don't like OpenDNS/Google DNS/CloudFlare DNS.

### vm.conf

This is the main VMM configuration file. It needs to be edited, according to your particular setup and whatever you want to achieve. The default setting does have all VMs disabled. It can be used as a clear example to _brew_ your own. 

Our VMs file, has comments, this is the main file, you need to edit this to your taste.

### Makefile usage

Assuming you have comp6X.tgz installed.

```
# cd /tmp
# ftp -V https://raw.githubusercontent.com/h3artbl33d/vmm_setup/master/Makefile
# make vmm

[+] Downloading files from https://github.com/h3artbl33d/vmm_setup

hostname.vether0	100% |*********************************************************|    39       00:00
dhcpd.conf		100% |*********************************************************|   420       00:00
vm.conf			100% |*********************************************************|   216       00:00
pf.conf			100% |*********************************************************|  1882       00:00
sysctl.conf		100% |*********************************************************|    25       00:00

[+] Installing files...

[+] Enabling vmd & setting dhcpd...

[+] All done, you can reboot now and play with vmm(4).

# reboot
```

### vmm.sh usage

```
# cd /tmp
# ftp -o - https://raw.githubusercontent.com/h3artbl33d/vmm_setup/master/vmm.sh | sh -
Trying 151.101.24.133...
Requesting https://raw.githubusercontent.com/h3artbl33d/vmm_setup/master/vmm.sh
1329 bytes received in 0.00 seconds (6.12 MB/s)

[+] Downloading files from https://github.com/h3artbl33d/vmm_setup

hostname.vether0	100% |*********************************************************|    39       00:00
dhcpd.conf		100% |*********************************************************|   420       00:00
vm.conf			100% |*********************************************************|   216       00:00
pf.conf			100% |*********************************************************|  1882       00:00
sysctl.conf		100% |*********************************************************|    25       00:00

[+] Installing files...

[+] Enabling vmd & setting dhcpd...

[+] All done, you can reboot now and play with vmm(4).

# reboot
```

### vmm(4) running
```
$ vmctl status
   ID   PID VCPUS  MAXMEM  CURMEM     TTY        OWNER NAME
    7 25839     1    512M    161M   ttyp1         root OpenBSD_Test.vm
    6 96312     1    2.0G    733M   ttyp0         root OpenBSD_Ports.vm
    3 30269     1    2.0G    401M   ttyp6         root Alpine_Linux.vm
```

```
$ doas vmctl console 7

OpenBSD/amd64 (test.vm.incre.host) (tty00)

login:
```

```
$ doas vmctl console 3


Welcome to Alpine Linux 3.7
Kernel 4.9.65-1-virthardened on an x86_64 (/dev/ttyS0)

box login: h3artbl33d
Password:
Welcome to Alpine!

The Alpine Wiki contains a large amount of how-to guides and general
information about administrating Alpine systems.
See <http://wiki.alpinelinux.org>.

You can setup the system with the command: setup-alpine

You may change this message by editing /etc/motd.

alpine:~$
```
### Credits

This repository and it's code was forked from [Gonzalo](https://github.com/gonzalo-). Much thanks and credits to him/her.
