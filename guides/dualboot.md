# Xiaomi POCO X3 Pro linux mainline guides

## Dualboot guide

### Prerequisites
- Custom recovery 

We will now setup a UEFI to boot our Mainline and also Android

>[!WARNING]
> We highly do not recommend using this UEFI to boot windows, As it has an outdated ACPI so drivers may not work properly or even at the worst cases cause hardware damage due to wrong panel configuration, You have been warned.

### Enter chroot if you exited from it
If you have ran "exit" or you rebooted your device or termux proccess ended somehow run this to reneter chroot
```sh
ch
```
If you are already in chroot and you do see a "root@localhost" or a "youruser@localhost" you can skip this

### Configuring simpleinit
> We will add a button to the edk2-msm UEFI boot menu called linux which will boot Mainline whenever clicked.

Install the config
```sh
sudo wget https://github.com/gixousiyq/POCO-X3-Pro-Mainline-Guides/releases/download/edk2-config/simpleinit.uefi.cfg -O /boot/simpleinit/simpleinit.uefi.cfg
```

### Flash edk2-msm UEFI
> After doing this step you will have a boot menu everytime you boot which you can select Android or Linux from

Grab uefi_installer from [here](https://github.com/woa-vayu/edk2-msm/releases/tag/huh):

- If you're panel is Hauxing download uefi-installer-vayu-huaxing.zip

- If you're panel is Tianma download uefi-installer-vayu-tianma.zip

After that boot your custom recovery and flash the file you downloaded and reboot

Voila! A boot menu appeared with Android and Linux as an option, Enjoy!
 
All credits goes to woa-vayu developers for providing this UEFI, Big thanks goes to them and thier amazing work
