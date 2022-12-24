+++
title = "General Asusctl Install"
description = "General steps to install asusctl on fedora, openSUSE"
sort_by = "none"
template = "page/wiki.html"
author = "Luke Jones"
+++

This is a very general and basic guide. If you intend to use Fedora or Arch please see the respective full-length articles under the wiki section.

You must first:
- update your distro
- install Nvidia drivers
- remove any distro provided methods of graphics switching (like prime)
- reboot!

## [fedora](#fedora)

```
$ dnf copr enable lukenukem/asus-linux
$ dnf install asusctl --refresh
```
then `Reboot!`

## [openSUSE](#opensuse)

Run:

```
$ zypper ar --priority 50 --refresh https://download.opensuse.org/repositories/home:/luke_nukem:/asus/openSUSE_Tumbleweed/ asus-linux
$ zypper ref
$ zypper in asusctl
```
then `Reboot!`

**NOTE:** 2021 hardware in general should be using Fedora not Ubuntu or derivatives as these lag far too long behind in kernel versions and graphics stacks. You need at least kernel 5.15 for minimal hardware support.

## [Other distros](#other-distros)

Are you willing to create and maintain packaging? Open an issue on the [website repo](https://gitlab.com/asus-linux/website/-/issues). Please see the [repository](https://gitlab.com/asus-linux/asusctl) for guidance.

# [Finished](#finished)

Reboot and congratulations, everything is running now. Using `asusctl --help` will provide some instructions on how/what to set up via CLI.

{{
  section_contribute(
    head="Has this guide helped you to setup asusctl?"
    text_content="The work on asusctl is a time consuming project. We do our best to keep up with supporting the newest ROG laptop models, reverse engineer, create kernel patches (so those features benifits all) and create tools to communicate with the various features of the hardware. We would love to see you coding with us - together we can make ASUS ROG laptops a first class Linux citizen!"
    text=true
    donate=true
  )
}}