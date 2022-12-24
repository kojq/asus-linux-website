  +++
title = "Fedora Silverblue Setup Guide"
description = "A Quickstart Guide to Fedora Silverblue and Asus-Linux"
sort_by = "none"
template = "page/wiki.html"
author = "Joseph Hindle"
+++

## Fedora Silverblue

The following process will help you to setup your Asus machine with Fedora Silverblue.

If you are installing on a Zephyrus G14 (2022), there is a guide [here](https://gist.github.com/jacobranson/adc32b6247f5c19494da0d3a24026d8f) which has lots of information.

### Installation

The user may exercise their own discretion with the options given, however be sure to decline the enabling of "third-party" repositories.


### Asus-Linux Repository

First we will add the Asus-Linux repository in order to install asusctl and supergfxctl.

Open a terminal window and enter

```
sudo nano /etc/yum.repos.d/asus.repo
```

We can then paste in the following with the key combination 'control + shift + v'

```
[copr:copr.fedorainfracloud.org:lukenukem:asus-linux]
name=Copr repo for asus-linux owned by lukenukem
baseurl=https://download.copr.fedorainfracloud.org/results/lukenukem/asus-linux/fedora-$releasever-$basearch/
type=rpm-md
skip_if_unavailable=True
gpgcheck=1
gpgkey=https://download.copr.fedorainfracloud.org/results/lukenukem/asus-linux/pubkey.gpg
repo_gpgcheck=0
enabled=1
enabled_metadata=1
```

We can save and exit with 'control + x'

### Nvidia

We can now start the process of acquiring and installing the NVIDIA drivers. 

```
rpm-ostree install --apply-live https://mirrors.rpmfusion.org/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm https://mirrors.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-$(rpm -E %fedora).noarch.rpm
```


```
rpm-ostree update \
            --uninstall rpmfusion-free-release-35-1.noarch \
            --uninstall rpmfusion-nonfree-release-35-1.noarch \
            --install rpmfusion-free-release \
            --install rpmfusion-nonfree-release         
```


Note that the specific rpmfusion release (e.g 35-1) will change with time. Use the command 'rpm-ostree status' to ensure you are removing the correct version in the above command.

```
rpm-ostree install akmod-nvidia xorg-x11-drv-nvidia-cuda xorg-x11-drv-nvidia-power asusctl 
```

```
rpm-ostree kargs --append=rd.driver.blacklist=nouveau --append=modprobe.blacklist=nouveau --append=nvidia-drm.modeset=1
```
 
```
systemctl reboot
```

```
systemctl enable nvidia-{suspend,resume,hibernate} 
```
```
systemctl enable --now supergfxd.service
```


```
systemctl mask nvidia-fallback.service
```


```
flatpak update
```



### Optional Optimisations 

#### Install the ROG Kernel

The ROG Kernel often contains patches that may provide benefit for the hardware you are running. The need for this will depend on your use case, your hardware and the current upstream status of these patches.

Unfortunately Silverblue is currently unable to automatically install a kernel from a COPR. Thus we will have to install and update this locally ourselves.

Head over **[here](https://copr.fedorainfracloud.org/coprs/lukenukem/asus-linux/package/kernel/)** and grab the following .rpm files from the latest build.

-	kernel
-	kernel-core
-	kernel-devel
-	kernel-modules
-	kernel-modules-extra

We can now use the following syntax to override the default kernel

```
rpm-ostree override replace <path_to_kernel> <path_to_kernel-core> <path_to_kernel-devel> <path_to_kernel-modules> <path_to_kernel-modules-extra>
```



 

#### Flatpak Cleaning

In order to streamline our dependency on flatpak it is worthwhile to  have everything working with the same fundamentals. 

```
flatpak remove --all 
flatpak remote-delete fedora 
flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo
flatpak update
``` 



#### Replace Firefox RPM with Flathub Flatpak and Force Wayland

```
rpm-ostree override remove firefox && flatpak install flathub org.mozilla.firefox flathub org.freedesktop.Platform.ffmpeg-full
```

```
sudo flatpak override --socket=wayland --env=MOZ_ENABLE_WAYLAND=1 org.mozilla.firefox
```

#### Enable hardware decoding in Firefox

Enter about:config into the Firefox address bar. Ensure that the following settings are matched in your setup.

_______
media.ffmpeg.vaapi.enabled = true

media.ffvpx.enabled = false

media.navigator.mediadatadecoder_vpx_enabled = true

media.rdd-vpx.enabled = false

________________

#### Flatseal and Steam

```
flatpak install flathub com.github.tchx84.Flatseal flathub com.valvesoftware.Steam
```



Open Flatseal and then add the following environment variables for Steam

```
__NV_PRIME_RENDER_OFFLOAD=1
```

```
__GLX_VENDOR_LIBRARY_NAME=nvidia
```

#### GNOME Shell extension asusctl-gex

A desktop widget to operate some aspects of asusctl and supergfxctl:
[GNOME Shell extension asusctl-gex](https://extensions.gnome.org/extension/4320/asusctl-gex/)

{{
  section_contribute(
    head="Has this guide helped you set up your machine?"
    text=true
    donate=true
  )
}}
