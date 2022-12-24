+++
title = "Reinstalling GRUB2"
description = "How to repair and reinstall a completely messed up GRUB2 in Fedora."
date = 2022-10-29
template = "page/default.html"
draft = false
+++

Somehow I managed to completely mangle grub2 to the point even grub's self-repair couldn't work things out.

The usual method of fixing grub2 is to `dnf reinstall grub2-common shim` and maybe some other packages however this wasn't helping at all. I also use `grub-btrfs` (which, now that I think of it, I could have used to repair in this scenario too 🤔).

Anyway, I can never find one guide that's complete or up-to-date on fixing a completely messed up grub2. So here it is. Mostly for myself when I do it the next time! 😊

_Note: This guide is intended for UEFI and Fedora systems only. The order is similar and can be adapted to other systems, if you know their peculiarities._

## Where is stuff?
Step 1. Boot from the Fedora Live CD...

First of all, run `lsblk` to remember your partition structure which you haven't looked at for 10 months. You can also use Gnome Disks:

```
> lsblk
nvme0n1     259:0    0   1.8T  0 disk 
├─nvme0n1p1 259:1    0   100M  0 part 
├─nvme0n1p2 259:2    0    16M  0 part 
├─nvme0n1p3 259:3    0 117.2G  0 part 
├─nvme0n1p4 259:4    0   707M  0 part 
├─nvme0n1p5 259:5    0   512M  0 part
└─nvme0n1p6 259:6    0   1.1T  0 part
```

## Get set up
Great, so now I remember `nvme0n1p6` is my Fedora install (Btrfs), and `nvme0n1p5` is my _Linux_ EFI partition (FAT32).

Note also here: My Windows has its nice own EFI partition—`nvme0n1p1` FAT32 of 100MB. If you have separate EFI partitions for Windows & Linux, they are unlikely to ever fight and kill each others' boot systems, and if they do, it's a lot simpler to fix it.

Let's mount my filesystem. Thankfully I no longer need to remember Btrfs commands, `mount` is smart enough to recognise it's mounting Btrfs.

```bash
# Make a folder to mount things; it doesn't matter what it's called:
sudo mkdir /mnt/sysimage

# Mount my main root '/' folder the new mountpoint:
sudo mount /dev/nvme0n1p6 /mnt/sysimage

# My /boot is also within that filesystem, so we'll tell chroot this:
sudo mount --bind /boot /mnt/sysimage/boot

# And then mount the EFI partition inside of /boot
sudo mount /dev/nvme0n1p5 /mnt/sysimage/boot/efi

# Tell chroot where other important folders are:
sudo mount --bind /dev /mnt/sysimage/dev
sudo mount --bind /proc /mnt/sysimage/dev
sudo mount --bind /sys /mnt/sysimage/sys
sudo mount --bind /run /mnt/sysimage/run

# And tell the kernel how to access to variables stored in NVRAM:
modprobe efivarfs
mount -t efivarfs efivarfs /mnt/sysimage/sys/firmware/efi/efivars
```

DNS won't work once you chroot, so we'll copy the Live CD resolv.conf so you have internets:
```bash
mkdir -p /mnt/sysimage/var/run/NetworkManager
cp /var/run/NetworkManager/resolv.conf /mnt/sysimage/var/run/NetworkManager
```

Okay, we are ready to switch control to your normal Fedora install! We do this with `chroot`:

`sudo chroot /mnt/sysimage`

## Resetting and reinstalling grub2
Now you're chroot, you no longer need to use `sudo` for things. You are `su` already. So excercise appropriate care.

For grub to completely regenerate itself, you need to clear several files.

**Important. If you've customised any files in `/etc/grub.d/` (for grub-btrfs, or chainloading, etc.) copy these files somewhere safe (e.g. `/home/<yourusername>/backup`. For the reinstall to work, only copy the minimum of files you are sure are correct/working.

```bash
# Delete everything except your /etc/default/grub config:
rm /etc/grub2/grub.cfg
rm /boot/efi/EFI/fedora/grub.cfg

rm /etc/grub.d/*
rm /etc/sysconfig/grub

# With all this gone, and everything mounted correctly, GRUB2 should now
# regenerate everything on reinstall:
dnf reinstall grub2-efi shim grub2-tools grub2-common
```

## Rebuilding all the grub configurations
_If you backed up any configs in /etc/grub.d/, copy them back now._

Now that all the original configs are reset, you should be able to build working grub2 configs:

`grub2-mkconfig -o /boot/grub2/grub.cfg`

_Note: On Feodra you would normally just use `grub2-mkconfig -o /etc/grub2.cfg`. However, we don't know for sure that the symlinks are working yet (see below), so we're creating the actual grub.cfg file first._

## Some final checks...
We're getting close. Lastly we'll check the symlinks in /etc/ are correct:
`ls -la /etc/grub2*`

They should look exactly like this:
```bash
lrwxrwxrwx. 1 root root 20 Oct 27 23:43 /etc/grub2.cfg -> /boot/grub2/grub.cfg
lrwxrwxrwx. 1 root root 22 Oct 12 13:24 /etc/grub2-efi.cfg -> ../boot/grub2/grub.cfg
```

If not, recreate them with:
```bash
rm /etc/grub2.cfg /etc/grub2-efi.cfg

ln -s /boot/grub2/grub.cfg /etc/grub2.cfg
ln -s /boot/grub2/grub.cfg /etc/grub2-efi.cfg
```

Great. Now list your grub2 menu options and make sure they look correct before you reboot. If there are some missing or wrong, check your `/etc/default/grub` config, and re-run `grub2-mkconfig`.

`efibootmgr`

If you want to be really sure GRUB2 was properly reinstalled, you can check the files we deleted were recreated:
```bash
cat /boot/efi/EFI/fedora/grub.cfg
cat /boot/grub2/grub.cfg
```

...and make sure those files both have contents. If they do, you should be all good!

## Unmount stuff
Exit the `chroot` then unmount things, before finally rebooting 😊:

_Note: You could probably get away with _not_ doing this, hoewever it makes sure the file system has actually written all changes to the drive before you reboot, especially for COW (copy-on-write) filesystems like Btrfs._

```bash
exit

sudo umount /mnt/sysimage/boot/efi
sudo umount /mnt/sysimage/sys/firmware/efi/efivars
sudo umount /mnt/sysimage/sys
sudo umount /mnt/sysimage/dev
sudo umount /mnt/sysimage/proc
sudo umount /mnt/sysimage/run
sudo umount /mnt/sysimage

reboot
```

Hopefully you can now boot! Good luck, let us know in the Discord if you have any problems! 😊

{{                                                                                                 
   section_contribute(                                                                              
     head="Has this post helped you?"                                                               
     text=true                                                                                      
     donate=true                                                                                    
   )                                                                                                
}}
