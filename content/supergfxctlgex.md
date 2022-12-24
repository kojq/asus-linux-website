+++
title = "supergfxctl-gex"
template = "page/default.html"
+++

## supergfxctl-gex

![screenshot.png](https://extensions.gnome.org/extension-data/screenshots/screenshot_5344_J6UR9TF.png)

supergfxctl-gex is a frontend for supergfxctl that was born inside the asus-linux.org community. supergfxctl is required for this extension to work. It is tested on a variaty of laptops including Intel / Nvidia, AMD / Nvidia, Intel / AMD and AMD / AMD GPU combinations.

It is a platform agnostic tool for hybrid graphics laptops.

supergfxctl gets packages by the community for Fedora and Arch. But without much of a hastle can be compiled for every systemd based Linux distribution.

## Requirements

* gnome >= 43
* [supergfxctl](https://gitlab.com/asus-linux/supergfxctl) >= 5.0.1

---

## Build Instructions

### Dependencies

* nodejs >= 16.0.0
* npm >= 6.14.0

### Building (production)

In a terminal enter the following commands as a user (**do NOT run as root or sudo**):

```bash
git clone https://gitlab.com/asus-linux/supergfxctl-gex.git /tmp/supergfxctl-gex && cd /tmp/supergfxctl-gex
npm install
npm run build && npm run install-user
```

_HINT: You will need to reload the GNOME Shell afterwards. (`Alt + F2` -> `r` on X11, `logout` on Wayland)_

### Building (development)

Instead of the
`npm run build && npm run install-user`
above, use this line instead:
`npm run build && npm run install-dev`

This will remove any production versions and installs the development version instead.

_HINT: You will need to reload the GNOME Shell afterwards. (`Alt + F2` -> `r` on X11, `logout` on Wayland)_ and probably manually enable the extension again.

### Source debugging

`cd` into the directory where you've downloaded the `supergfxctl-gex` source code and enter the following commands:

```bash
npm install
npm run debug
```

---

## License & Trademarks

**License:** Mozilla Public License Version 2.0 (MPL-2)