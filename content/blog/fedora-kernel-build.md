+++
title = "Build a fedora kernel: Updated"
description = "Patches you might require won't always be backported - this post will help you build a kernel with the required patches"
date = 2022-08-02
template = "page/default.html"
draft = false
+++

The primary source of information on building a custom kernel in fedora is [here](https://fedoraproject.org/wiki/Building_a_custom_kernel#Building_a_Kernel_from_the_Fedora_source_tree). This post will step you through that with an additional step to add the patches you may need.

## Get the source

First, where-ever you feel like, clone the kernel packaging source, for example:

```bash
cd ~ && mkdir projects && cd ~/projects
fedpkg clone -a kernel
```

Then cd in to the new directory and checkout the required branch matching the fedora release number you need:

```bash
cd ~/projects/kernel
git checkout origin/f36
```

To save work with git (not a compulsory step, but will help with updating), do:
```
git checkout -b patch-f36
git branch -u origin/f36
```

## Prepare for build

### Install deps

Run:
```
sudo dnf install fedpkg fedora-packager rpmdevtools ncurses-devel pesign grubby
sudo dnf builddep kernel.spec
```

### Edit kernel.spec

In `kernel.spec` is where you will do most of the work, so open up that file in your
favourite text editor which is vim and search for this line:

```bash
# define buildid .local
```

and replace it with:

```bash
%define buildid .local
```

then a few hundred or so lines further down the file you will find a block like this:

```bash
%if !%{nopatches}

Patch1: patch-%{patchversion}-redhat.patch
%endif
```
This is where you will need to add patches, an example is adding the `asus-hid` driver
patch required for ASUS laptops with the 0x1866 device (N-Key Keyboard). So below the
line above add:

```bash
%if !%{nopatches}

Patch1: patch-%{patchversion}-redhat.patch

# ROG Laptops
Patch500: 0001-WMI-asus-Reduce-G14-and-G15-match-to-min-product-nam.patch
Patch501: 0001-HID-asus-Filter-keyboard-EC-for-old-ROG-keyboard.patch
Patch502: 0001-HID-asus-Add-support-for-2021-ASUS-N-Key-keyboard.patch
%endif
```

and lastly, the patches must be in the same directory as `kernel.spec`

You need to also add them before the `# END OF PATCH APPLICATIONS`
line in the existing block like-so:
```
%if !%{nopatches}

ApplyOptionalPatch patch-%{patchversion}-redhat.patch

ApplyOptionalPatch 0001-WMI-asus-Reduce-G14-and-G15-match-to-min-product-nam.patch
ApplyOptionalPatch 0001-HID-asus-Filter-keyboard-EC-for-old-ROG-keyboard.patch
ApplyOptionalPatch 0001-HID-asus-Add-support-for-2021-ASUS-N-Key-keyboard.patch
%endif
```

and then build using `fedpkg --release f36 local --without debug --without debuginfo --with release` (remember to replace f36 with the branch you checked out).

To customize a kernel, edit `kernel-local`  for example:

If building from a third-party repo you may require `--name kernel --namespace rpms` (after `fedpkg`).

```
# This file is intentionally left empty in the stock kernel. Its a nicety
# added for those wanting to do custom rebuilds with altered config opts.
CONFIG_PINCTRL_AMD=y
```

## Install

All the rpm packages will be located in `~/projects/kernel/x86_64/`, it's likely that
you will need to install all except the debug packages.

## Updating

If you have any work to save, `git commit` it, or `git stash`.

```
git pull
```

then `git stash pop` if you stashed work.

{{
  section_contribute(
    head="Has this post helped you?"
    text=true
    donate=true
  )
}}