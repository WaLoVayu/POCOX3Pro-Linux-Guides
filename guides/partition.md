# Partitioning your POCO X3 Pro

## Files/Tools Needed

Modified TWRP:

| File Name                                       | Android version |
|-------------------------------------------------|-----------------|
| [twrp-3.7.1_vap.img](https://github.com/WaLoVayu/POCOX3Pro-Windows-Guides/releases/download/twrp/twrp-3.7.1_vap.img) | Android 12/12.1/13/14/15 |

- Unlocked bootloader

- [ADB & Fastboot](https://developer.android.com/studio/releases/platform-tools)

- [Termux](https://play.google.com/store/apps/details?id=com.termux)

- [Latest POCO X3 Pro firmware](https://github.com/WaLoVayu/POCOX3Pro-Windows-Guides/releases/download/firmware/fw_vayu_miui_VAYUGlobal_V14.0.3.0.TJUMIXM_6be397120d_13.0.zip)

### Notes
>
> [!WARNING]  
> All your data will be erased! Back up now if needed.
>
> Do not run the same command twice.
>
> DO NOT REBOOT YOUR PHONE if you think you made a mistake, ask for help in the [Telegram chat](https://t.me/WaLoVayu).
>
> Do not run all commands at once, execute them in order!

> [!IMPORTANT]
> Make sure you use the MODDED Recovery throughout this whole tutorial as we provide tools to help aid this installation process and make it as easy as possible for you.
>
> If you don't use it and you face any errors, consider it your fault and consider yourself alone if you try asking for support as you have deferred from the main guide.

### Opening CMD as an admin
> Download **platform-tools** and extract the folder somewhere, then open CMD as an **administrator**.
>
> It is recommended to keep this window open and use it throughout the entire guide.
> 
> Replace `path\to\platform-tools` with the actual path to the platform-tools folder, for example **C:\platform-tools**.
```cmd
cd path\to\platform-tools
```

### Flash and boot into the modified TWRP
> While your device is in fastboot mode, replace `path\to\modded-twrp.img` with the actual path of the provided TWRP image
```cmd
fastboot flash recovery path\to\modded-twrp.img reboot recovery
```

### Flashing latest firmware
>
> [!Important]
> It is mandatory to flash the latest firmware (this does not affect your current ROM).

- Download the **fw_vayu_miui_VAYUGlobal_V14.0.3.0.TJUMIXM_6be397120d_13.0.zip** and put it somewhere on your phone.
- Select the **Install** button in TWRP, locate the firmware file, then install it.
- There is no need to reboot yet, stay in TWRP for the next few steps.

### Unmount data
> Ignore any errors such as "Invalid argument" and continue
```cmd
adb shell umount /dev/block/by-name/userdata
```

### Resize partition table
``` cmd
adb shell sgdisk --resize-table 64 /dev/block/sda
```

### Preparing for partitioning
```cmd
adb shell parted /dev/block/sda
```

### Print the current partition table
> Parted will print a list of partitions on your phone. You will see, in this order, the **partition number**, **start value**, **end value**, **partition size**, **filesystem**, and the **partition name**
```cmd
print
```

### Removing userdata
> Replace **$** with the actual number of **userdata** (it should be **32**)
```cmd
rm $
```

### Recreating userdata
> Replace **150GB** with the actual value you want to allocate to Android. In this example you will have 150GB-11.7GB = **138GB** of usable space
```cmd
mkpart userdata ext4 11.7GB 150GB
```

### Creating ESP partition
> Replace **150GB** with the actual end value you used for **userdata**
>
> Replace **151GB** with the same value + **1GB**
```cmd
mkpart esp fat32 150GB 151GB
```

### Creating linux partition
> Replace **151GB** with the actual end value you used for **esp**
```cmd
mkpart linux ext4 151GB -0MB
```

### Making ESP bootable
> Replace **$** with the actual partition number of **esp**, which should be **33**
```cmd
set $ esp on
```

### Exit parted
```cmd
quit
```

### Formatting data
> Format all data in TWRP, or Android will not boot.
- ( Go to **Wipe** > **Format data** > type **yes** )

### Reboot your device
> To check if Android still works
```cmd
adb reboot
```
> After it finishes booting, set up your device and root it if you haven't already

### Installing Termux
- Download and install **Termux** and grant it root access.


## [Next step: Installing a Linux distro](distro-selection.md)
