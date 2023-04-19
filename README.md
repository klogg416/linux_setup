# linux_setup
Notes and commands for a fresh install


# ubuntu fresh install

```shell
sudo apt update && sudo apt upgrade -y
sudo timedatectl set-timezone America/Toronto
sudo apt remove snapd ubuntu-advantage-tools -y
sudo apt install kbd libdrm-common libdrm2 python3-software-properties software-properties-common -y
sudo apt autoremove -y
sudo reboot
```

# if an unplugged network adaptor delays boot
Need to make NICs optional in `netplan`. See which files are in `/etc/netplan`, then edit the one in there.
```shell
$ ll /etc/netplan/
total 20
drwxr-xr-x   2 root root  4096 Apr 17 12:27 ./
drwxr-xr-x 131 root root 12288 Apr 18 07:38 ../
-rw-r--r--   1 root root   189 Apr 17 12:27 00-installer-config.yaml
```

Use `ip a` to get network adaptor ID, then at `optional: true` for each adaptor.

My current file looks like this:  
`cat  /etc/netplan/00-installer-config.yaml`
```shell
$ cat /etc/netplan/00-installer-config.yaml
# This is the network config written by 'subiquity'
network:
  ethernets:
    enp5s0:
      dhcp4: true
      optional: true
    enp7s0:
      dhcp4: true
      optional: true
  version: 2
  ```
  
# carrying on
```shell
sudo apt install docker.io docker-compose cifs-utils unattended-upgrades samba glances
sudo groupadd docker
sudo usermod -a -G docker kyle

# configure up unattended upgrades
sudo nano  /etc/apt/apt.conf.d/50unattended-upgrades
sudo nano /etc/apt/apt.conf.d/10periodic

# install nvidia GPU drivers
# list available drivers, select the "recommended" one
ubuntu-drivers devices
sudo apt install nvidia-driver-525-open nvidia-cuda-toolkit
```

`ubuntu-drivers devices` output
```shell
$ ubuntu-drivers devices
== /sys/devices/pci0000:00/0000:00:03.1/0000:08:00.0 ==
modalias : pci:v000010DEd00002184sv00001462sd00003790bc03sc00i00
vendor   : NVIDIA Corporation
model    : TU116 [GeForce GTX 1660]
driver   : nvidia-driver-525 - distro non-free
driver   : nvidia-driver-470 - distro non-free
driver   : nvidia-driver-418-server - distro non-free
driver   : nvidia-driver-515-server - distro non-free
driver   : nvidia-driver-515-open - distro non-free
driver   : nvidia-driver-510 - distro non-free
driver   : nvidia-driver-450-server - distro non-free
driver   : nvidia-driver-525-server - distro non-free
driver   : nvidia-driver-470-server - distro non-free
driver   : nvidia-driver-525-open - distro non-free recommended
driver   : nvidia-driver-515 - distro non-free
driver   : xserver-xorg-video-nouveau - distro free builtin
```

# free up port 53
Details <https://wiki.klaynation.com/books/cli-and-systems/page/ubuntu-dns-failure-freeing-up-port-53>

Is something using port `53`?
```shell
sudo lsof -i :53
sudo nano /etc/systemd/resolved.conf
```

Uncomment `DNS=` and add a DNS server
Uncomment `DNSStubListener=no` and set to `no`

```shell
$ cat /etc/systemd/resolved.conf
#  This file is part of systemd.
#
#  systemd is free software; you can redistribute it and/or modify it under the
#  terms of the GNU Lesser General Public License as published by the Free
#  Software Foundation; either version 2.1 of the License, or (at your option)
#  any later version.
#
# Entries in this file show the compile time defaults. Local configuration
# should be created by either modifying this file, or by creating "drop-ins" in
# the resolved.conf.d/ subdirectory. The latter is generally recommended.
# Defaults can be restored by simply deleting this file and all drop-ins.
#
# Use 'systemd-analyze cat-config systemd/resolved.conf' to display the full config.
#
# See resolved.conf(5) for details.

[Resolve]
# Some examples of DNS servers which may be used for DNS= and FallbackDNS=:
# Cloudflare: 1.1.1.1#cloudflare-dns.com 1.0.0.1#cloudflare-dns.com 2606:4700:4700::1111#cloudflare-dns.com 2606:4700:4700::1001#cloudflare-dns.com
# Google:     8.8.8.8#dns.google 8.8.4.4#dns.google 2001:4860:4860::8888#dns.google 2001:4860:4860::8844#dns.google
# Quad9:      9.9.9.9#dns.quad9.net 149.112.112.112#dns.quad9.net 2620:fe::fe#dns.quad9.net 2620:fe::9#dns.quad9.net
DNS=1.1.1.1
#FallbackDNS=
#Domains=
#DNSSEC=no
#DNSOverTLS=no
#MulticastDNS=no
#LLMNR=no
#Cache=no-negative
#CacheFromLocalhost=no
DNSStubListener=no
#DNSStubListenerExtra=
#ReadEtcHosts=yes
#ResolveUnicastSingleLabel=no
```

```shell
sudo ln -sf /run/systemd/resolve/resolv.conf /etc/resolv.conf
sudo reboot
```

# working with a disk array

```shell
# see array build / repair progress
cat /proc/mdstat

# display min and max speeds for rebuild.
sysctl dev.raid.speed_limit_min
sysctl dev.raid.speed_limit_max

# increase min speed
sudo sysctl -w dev.raid.speed_limit_min=50000 

# confirm increase took
sysctl dev.raid.speed_limit_min
```

# install a web dashboard
```shell
sudo apt install -t ${VERSION_CODENAME}-backports cockpit
sudo apt install cockpit-machines pcp cockpit-pcp packagekit virt-viewer
sudo systemctl enable --now cockpit.socket
sudo systemctl enable --now pmcd
sudo systemctl enable --now pmlogger
```
  
# nvidia GPU with plex
<https://tizutech.com/plex-transcoding-with-docker-nvidia-gpu/>
Document it.
