# Installing Ubuntu on your POCO X3 Pro

## Requirements
- Working Android

- Rooted Android

- Partitioned device. If you dont know what partitions do you need, follow [partitioning guide](/common/partition.md)

### Preparing the environment needed to build our system
Install [Termux](https://github.com/termux/termux-app/releases/download/v0.118.1/termux-app_v0.118.1+github-debug_arm64-v8a.apk)

### Installing required packages
Run this to install required packages throughout the guide
```sh
apt update && apt upgrade -y && apt install wget tsu
```
During this command it may ask you some questions, Answer all of them with "y", If it didn't it's not an issue, You can proceed with the guide safely

#### Downloading base rootfs
> This is the rootfs that we will build our system on

Run this to download it
```sh
wget https://cdimage.ubuntu.com/ubuntu-base/releases/24.04/release/ubuntu-base-24.04.1-base-arm64.tar.gz
```

### Mounting Linux partition 
> We will now mount linux ext4 partition we created earlier so that we can build our system there.
```sh
sudo mkfs.ext4 /dev/block/by-name/linux
sudo mkfs.fat /dev/block/by-name/esp -F32 -s1 -n esp
mkdir linux
su -c mount -t ext4 /dev/block/by-name/linux linux
```

### Applying base rootfs 
> We will now apply base rootfs to the linux partition we mounted.

Run this to enter root shell
```sh
tsu
```
Then this to apply the rootfs
```sh
tar xvf ../ubuntu-base-24.04.1-base-arm64.tar.gz -c linux
```
Then you can exit the root shell
```sh
exit
```

### Entering Chroot
Now we will download the script that will lets us enter chroot
```sh
wget https://github.com/remtrik/x3p_mainline/raw/main/files/ch -O $PREFIX/bin/ch && chmod +x $PREFIX/bin/ch
```

And now run this anywhere in termux
```sh
ch
```

And you are now inside the system! But we are not finished yet, There's still alot of work to do so stick with me a little bit.

### Basic setup
> We will now run some commands to fix errors and warnings such as:

- apt cannot resolve host
```sh
echo "nameserver 8.8.8.8" > /etc/resolv.conf
echo "127.0.0.1 localhost
127.0.0.1 xiaomi-vayu" > /etc/hosts
```
- Download is performed unsandboxed as root
```sh
groupadd -g 3003 aid_inet
groupadd -g 3004 aid_net_raw
groupadd -g 1003 aid_graphics
usermod -g 3003 -G 3003,3004 -a _apt
usermod -G 3003 -a root
```
Run these now! Not when the errors shows up.

Now we will tell the system our machine name
```sh
echo "xiaomi-vayu" > /etc/hostname
```

Now you can install packages and update the system

During this command it may ask you some questions, Answer all of them with "y"
```sh
apt update && apt upgrade -y && unminimize 
```
>[!WARNING]
>If you are planning to use grub, Remove u-boot-tools- from the command bellow!!! It breaks grub installation!!!

Install general packages
```sh
apt install u-boot-tools- nano net-tools sudo git bash-completion ssh rmtfs protection-domain-mapper tqftpserv linux-firmware
```

### Creating a user
We will now make a user that will carry your username in Ubuntu 

Now replace "vayu" bellow with the username you want, If you are unsure what username you want you can leave it as is, It does **NOT** need to be lowercase by the way; Make sure to memorize it or write it somewhere if you can't remember it because we will need it later
```sh
export USER=vayu 
```
Now run this to create the user you entered its name, Just paste this in termux. Then it will ask you to enter a password, It seems that typing in there doesn't do anything but in reality it does but it's just hidden.
```sh
groupadd storage
groupadd wheel
useradd -m -g users -G wheel,audio,video,storage,aid_inet -s /bin/bash $USER
passwd $USER
```
Now we have to add your user to the sudoers list, Otherwise you wont be able to execute sudo

Type this command
```
nano /etc/sudoers
```
A text editor called nano will show up, Scroll via your fingers until you see the line that says 
```
root ALL=(ALL:ALL) ALL
```
Add a line below it like this

Replace "user" with your username that you typed earlier 
```
user ALL=(ALL:ALL) ALL
```
So at the end it should **FOR EXAMPLE** look like this **if you used the default user, as an example**
```
root ALL=(ALL:ALL) ALL
vayu ALL=(ALL:ALL) ALL
```
Now press CTRL + S to save and CTRL + X to exit

Now enter as the user you created using this command
```sh
su theuseryoucreated
cd
```
Now we will install a desktop environment

### Installing a desktop environment 
We will now install the desktop that we will use everytime we boot Ubuntu, So pick the one you want wisely!

Run this command to install Ubuntu desktop (default Ubuntu distribution desktop)
```sh
sudo apt install -y ubuntu-desktop-minimal
```
You can also replace ubuntu-desktop-minimal with:
- ubuntu-desktop ```Full of bloatware, Heats the system on idle```
- plasma-mobile sddm sddm-theme-breeze ```KDE Plasma Mobile```
- kubuntu-desktop ```KDE Plasma, UNTESTED``` 
- xfce4 ```UNTESTED```
  
And many more

### Create fstab
fstab file is mandatory in Linux systems; It tell's the operating system what to mount and where, Without it the system wont boot.

To create it run the command bellow
```sh
echo "PARTLABEL=linux / ext4 errors=remount-ro,x-systemd.growfs 0 1
PARTLABEL=esp /boot vfat umask=0077 0 1
PARTLABEL=logfs /simpleinit vfat umask=0077 0 1" | sudo tee /etc/fstab
```

### Getting the panel model
POCO X3 Pro has 2 panels, One is called Huaxing and one is called Tianma; You need to know which one you have, Picking the definitions of the other panel may be catastrophic and may cause permanent damage!!!!! 

Run this command to determine what panel you have
```sh
echo $(sudo cat /proc/cmdline |  tr " :=" "\n" | grep dsi) | tr " " "\n" | tail -1
```

- dsi_j20s_42_02_0b_video_display: Means you have Hauxing panel

- dsi_j20s_36_02_0a_video_display: Means you have Tianma panel

### Installing the mainline kernel and other device specific things

Grab:
- vmlinuz-6.12-rc6

- dtb-huaxing-6.12-rc6 ```If you have Hauxing panel```

- dtb-tianma-6.12-rc6 ```If you have Tianma panel```

- modules.tar.gz

- firmware.tar.gz

- HiFi.conf

- sm8150.conf

From [this link](https://github.com/remtrik/x3p_mainline/tree/main/files/vayu)

Once again we will assume they are in /storage/emulated/0/Download

Run this command to move them to the correct place
```sh
sudo mv /sdcard/Download/*-6.12-rc6 /boot/EFI
```
If you have Huaxing run this command
```sh
cd /boot/EFI
sudo mv dtb-huaxing-6.12-rc6 dtb-6.12-rc6
```
If you have Tianma run this command
```sh
cd /boot/EFI
sudo mv dtb-tianma-6.12-rc6 dtb-6.12-rc6
```
And this to unpack modules, The kernel won't boot without them!!!
```sh
sudo mkdir /lib/modules
cd /lib/modules
sudo tar xvf /sdcard/Download/modules.tar.gz
```

Run this command 
```sh
sudo pacman -S git --noconfirm
git clone https://github.com/sm8150-mainlining/firmware-xiaomi-vayu.git
sudo cp -r firmware-xiaomi-vayu/lib/firmware/* /lib/firmware
sudo mkdir -p /usr/share/qcom
sudo cp -r firmware-xiaomi-vayu/usr/share/qcom/* /usr/share/qcom
```

Run this command
```sh
sudo mkdir -p /usr/share/alsa/ucm2/conf.d/sm8150
sudo mkdir -p /usr/share/alsa/ucm2/conf.d/sdm845
sudo mkdir -p /usr/share/alsa/ucm2/Xiaomi/vayu
cd /usr/share/alsa/ucm2
sudo mv /sdcard/Download/HiFi.conf Xiaomi/vayu/HiFi.conf
sudo mv /sdcard/Download/vayu.conf Xiaomi/vayu/vayu.conf
sudo ln -sf /usr/share/alsa/ucm2/Xiaomi/vayu/vayu.conf conf.d/sm8150/xiaomi-vayu.conf
sudo ln -sf /usr/share/alsa/ucm2/Xiaomi/vayu/vayu.conf conf.d/sdm845/xiaomi-vayu.conf
sudo chown -R $(whoami) /usr/share/alsa
```

### Making initrd.img

To generate it run this command
```sh
cd /boot/EFI
sudo mkinitramfs -o initrd.img-6.12-rc6 6.12.0-rc6-sm8150+
```
If you see ```Possible missing firmware``` or ```/dev/disk/by-partlabel/linux doesn't exist``` or ```cannot check for zstd compression support``` or ```root device does not exist```  They are safe to ignore.


Now we are basically done! We have successfully built our Ubuntu system with its kernel and userspace, But always remember; The kernel cant boot itself, So now we will set up a UEFI or a bootloader that will allow us to boot our system, and android.

## [Next step: Setting up dualboot](/guides/dualboot.md)
