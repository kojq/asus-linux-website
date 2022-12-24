+++
title = "Welcome"
sort_by = "none"
template = "index/home.html"
insert_anchor_links = "left"
+++

Asus-Linux.org is an independent community effort that works to improve Linux support for Asus notebooks.

Many but not all ASUS ROG laptops work under Linux quite well. But there are always new models coming out, or older models that got missed. This project aims to solve all the issues you may have, and enable many ROG specific features.

Sound issues are the most common problem , and solving these requires a little more effort and patience - see the blog post about this plus visit the discord for guidance. Do note that the root problem is almost never pulse audio or pipewire - it's the Realtek codec chip needing specific fixes in the kernel driver.

The second most common issue is that of device feature enablement such as:
- AniMe Matrix display
- RGB keyboard LEDs
- fn+key combos and media controls
- G-Sync enable on capable laptops
- Re-enable the dGPU after setting "iGPU On" in Windows
- and more
All of the above is either enabled in [`asusctl`](https://gitlab.com/asus-linux/asusctl) already, or is enabled via patches to the kernel (and we provide pre-built kernels with the patches applied) which are steadily upstreamed.

Extra features we currently support are:
* Control graphics modes via `supergfxctl` without reboots for switching between iGPU, dGPU, on-demand, and vfio (for VM pass-through)
* Set battery charge limit
* (custom) fan curve control

{{
  section_contribute(
    text=true
    donate=true
  )
}}

