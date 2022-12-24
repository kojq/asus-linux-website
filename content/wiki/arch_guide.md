+++
title = "Arch Setup Guide"
description = "A simple guide for getting Arch running on ASUS laptops"
sort_by = "none"
template = "page/wiki.html"
author = "Mateusz Schyboll"
+++

# Installing

To install Arch on all ASUS laptops just follow the regular [installation guide](https://wiki.archlinux.org/title/installation_guide)

Only issue which might appear is nouveau crashing the installation, this can be solved by adding a boot parameter `modprobe.blacklist=nouveau` to the kernel cmdline before booting the installation media.
To edit the installation media boot entry just press e on it and then put the blacklist parameter at the end off all parameters. Example:

![Blacklist nouveau](/images/guide_arch_blacklist_nouveau.png)

The same parameter can be used to boot the system after installing as long you don't install nvidia drivers.

## Repo

g14 repo contains all the tools you need on a ROG laptop precompiled for you.
g14 is only a name and all tools from it apply to most ROG laptops
To get the repo add to your `/etc/pacman.conf` at the end:

```
[g14]
SigLevel = DatabaseNever Optional TrustAll
Server = https://arch.asus-linux.org
```

After adding the repo run a full system update before you go to install tools from the repo:
```
pacman -Suy
```


## Asusctl - custom fan profiles, anime, led control etc.

The recommended way to install asusctl is using g14 pacman repo. Packages like asusctl-git from AUR aren't supported.
Also installing manually from cloned git isn't supported. For installing it run:

```
pacman -S asusctl
```

asusd service is triggered by a udev rule after the keyboard driver is ready, the service doesn't need to be enabled and is not supposed to be.

asusctl needs power-profiles-daemon installed and running. It gets installed automatic when installing asusctl but you need to enable it:
```
systemctl enable --now power-profiles-daemon.service
```
Be aware that some functions or asusctl need kernel level drivers support, take a look at the "Custom kernel section"

## Supergfxctl - graphics switching

The same rules as for asusctl, installing:
```
pacman -S supergfxctl
```
Enable and start the systemd service:
```
systemctl enable --now supergfxd
```
supergfxctl can be used without asusctl. Currently installing asusctl installs supergfxctl as a dependency but that will be changed in the future.

## Custom kernel - drivers fixes, hardware support

Newer devices often require custom kernel with patches, that kernel is also available in the g14 pacman repo, to install it just run:
```
pacman -Sy linux-g14 linux-g14-headers
```
Again, don't get confused by the name, it exists only for historical reasons.
If you are using a custom kernel use the `nvidia-dkms` package for nvidia drivers, the regular `nvidia` package works only with stock Arch kernel
Currently all models needs a custom kernel, starting with 5.15 2020 models will only miss custom fan curve option.

There is also a xanmod kernel with all the patches needed [xanmod-rog](https://github.com/arglebargle-arch/xanmod-rog-PKGBUILD) which might give you better performance in games.

After installing the new kernel you need to regenerate your boot menu or add a new boot entry depending on what boot manager you are using. For GRUB that will be:
```
grub-mkconfig -o /boot/grub/grub.cfg
```
For others refer to their documentation/Arch Wiki page. You can check currently booted kernel with command `uname -r`. It should give you for example:
```
5.14.9-arch2-g14-1
```
The `-g14` part is the important one.

# Other distros based on Arch

To ease the installation you can use other distro based on Arch, only Manjaro is highly not recommend (it is not really based on Arch and might not be compatible with points above and things might break with upgrades).

Recommended ones are: EndeavourOS, RebornOS, Garuda.

Blacklisting nouveau might be still needed.

# EndeavourOS

When installing EndeavourOS don't use the option with Nvidia drivers preinstalled, this installs configs with might conflict with supergfxctl and installs the driver with works only with stock kernel. Use the default install option then install `nvidia-dkms` post install.

{{
  section_contribute(
    head="Has this guide helped you set up your machine?"
    text=true
    donate=true
  )
}}
