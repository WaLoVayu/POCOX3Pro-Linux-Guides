# Running Mainline on the POCO X3 Pro

## Partitioning your device

### Prerequisites
- Unlocked bootloader

- [ADB & Fastboot](https://developer.android.com/studio/releases/platform-tools)

- [Modified TWRP]()

- [Termux](https://play.google.com/store/apps/details?id=com.termux)

### Notes
> [!WARNING]  
>  
> Do not run all commands at once, execute them in order!
>
> YOU CAN BREAK YOUR DEVICE WITH THE COMMANDS BELOW IF YOU DO THEM WRONG!!!
>
> DO NOT REBOOT YOUR PHONE! If you think you made a mistake, ask for help in the [Telegram chat](https://t.me/mainlineonvayu).

### Opening CMD as an admin
> Download **platform-tools** and extract the folder somewhere, then open CMD as an **administrator**.
>
> It is recommended to keep this window open and use it throughout the entire guide.
> 
> Replace `path\to\platform-tools` with the actual path to the platform-tools folder, for example **C:\platform-tools**.
```cmd
cd path\to\platform-tools
```

#### Flash and boot into the modified TWRP
> While your device is in fastboot mode, replace `path\to\modded-twrp.img` with the actual path of the provided TWRP image
```cmd
fastboot flash recovery path\to\modded-twrp.img reboot recovery
```

### Back up your data
> Your data will be lost in the next few steps, so back it up if needed.

#### Unmount data
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

#### Print the current partition table
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

#### Exit parted
```cmd
quit
```

### Formatting data
> Format all data in TWRP, or Android will not boot.
- ( Go to **Wipe** > **Format data** > type **yes** )

#### Reboot your device
> To check if Android still works
```cmd
adb reboot
```
> After it finishes booting, set up your device and root it if you haven't already

### Installing Termux
- Download and install **Termux** and grant it root access.


## [Next step: Installing a Linux distro](distro-selection.md)
