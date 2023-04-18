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

# If an unplugged network adaptor delays boot
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
  
Carrying on
```shell
sudo apt install docker.io docker-compose cifs-utils
sudo groupadd docker
sudo usermod -a -G docker kyle
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
  
  
