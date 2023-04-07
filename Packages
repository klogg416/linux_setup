# ubuntu fresh install from Sean

sudo apt update && sudo apt upgrade -y

sudo dpkg-reconfigure tzdata

# do not trust ubuntu to update firmware
sudo apt remove snapd ubuntu-advantage-tools fwupd -y

sudo apt install kbd libdrm-common libdrm2 python3-software-properties software-properties-common -y
sudo apt autoremove -y

# I didn't do any of this
sudo apt install linux-lowlatency-hwe-22.04 fdutils ebtables -y
sudo update-alternatives --config iptables
sudo update-alternatives --config ebtables
sudo apt remove linux-generic -y 

sudo apt autoremove -y
sudo reboot
