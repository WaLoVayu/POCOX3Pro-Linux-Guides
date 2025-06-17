# Dualboot guide

### Prerequisites
- Custom recovery 

We will now setup a UEFI to boot our Mainline and also Android

>[!WARNING]
> We highly do not recommend using this UEFI to boot windows, As it has an outdated ACPI so drivers won't work or even at the worst cases cause hardware damage due to wrong panel configuration.

### Enter chroot if you exited from it
If you have ran "exit" or you rebooted your device or termux proccess ended somehow run this to reneter chroot
```sh
ch
```
If you are already in chroot and you do see a "root@localhost" or a "youruser@localhost" you can skip this

### Configuring simpleinit

Install the config
```sh
sudo wget https://raw.githubusercontent.com/WaLoVayu/POCOX3Pro-Linux-Guides/refs/heads/main/files/simpleinit.uefi.cfg -O /boot/simpleinit/simpleinit.uefi.cfg
```

### Flash edk2-msm UEFI
> After doing this step you will have a boot menu everytime you boot which you can select Android or Linux from

Grab uefi_installer from [here](https://github.com/WaLoVayu/edk2-msm/releases/tag/huh):

- If you have Huaxing download uefi-installer-vayu-huaxing.zip

- If you have Tianma download uefi-installer-vayu-tianma.zip

After that boot your custom recovery and flash the file you downloaded and reboot

Voila! A boot menu appeared with Android and Linux as an option, Enjoy!
