# Documentation for Resolving Slow Boot Time Due to Floppy Drive Error

## Introduction

This documentation provides steps to resolve slow boot times caused by the system attempting to read from a nonexistent floppy drive. Additionally, it covers updating the swap partition UUID and configuring GRUB to prevent floppy drive probing.

## 1. Identifying the Problem

During boot, the system repeatedly attempts to read from `/dev/fd0`, a floppy disk drive, resulting in the following errors: 

I/O error, dev fd0, sector 0 op 0x0:(READ) flags 0x0 phys_seg 1 prio class 0
floppy: error 10 while reading block 0

checked the boot logs through the following command:-
```
dmesg | less
```


## 2. Disabling Floppy Drive Probing

### 2.1 Disable Floppy Drive in BIOS/UEFI

1. Reboot the system and enter the BIOS/UEFI settings.  
2. Locate the option related to the floppy drive and disable it.  
3. Save the changes and reboot the system.

### 2.2 Blacklist the Floppy Module

1. Create a file to blacklist the floppy module:  
    ```bash
    echo 'blacklist floppy' | sudo tee /etc/modprobe.d/blacklist-floppy.conf
    ```
2. Update the initial RAM filesystem:  
    ```bash
    sudo update-initramfs -u
    ```
3. Reboot the system.

## 3. Updating GRUB Configuration

To ensure GRUB does not probe for the floppy drive during boot, follow these steps:

1. Open the GRUB configuration file:  
    ```bash
    sudo nano /etc/default/grub
    ```
2. Add or modify the following line:  
    ```bash
    GRUB_CMDLINE_LINUX='floppy.allowed_drive_mask=0'
    ```
3. Update GRUB:  
    ```bash
    sudo update-grub
    ```
4. Reboot the system.

## 4. Updating the Swap Partition UUID
As i did some changes to disk partitions:-
 
After resizing the partitions, the UUID of the swap partition may have changed. To update the system to recognize the new UUID:

1. Find the new UUID of the swap partition:  
    ```bash
    sudo blkid
    ```
2. Edit the `/etc/fstab` file:  
    ```bash
    sudo nano /etc/fstab
    ```
3. Update the swap entry with the new UUID:  
    ```bash
    UUID=your-new-swap-uuid none swap sw 0 0
    ```
4. Save the file and reboot the system.

## Conclusion

By following these steps, the system should no longer attempt to read from a nonexistent floppy drive, and the correct swap partition will be utilized, leading to faster boot times.
