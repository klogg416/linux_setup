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
