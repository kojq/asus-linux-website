+++
title = "asusctl-gex"
template = "page/default.html"
+++

## asusctl-gex is inactive

The current version of asusctl-gex is not feature complete with the current asusctl version so I set the extension as inactive for the time being.
We currently don't have the time to update and maintain it.

If you are familiar with TypeScript / JavaScript and have the time and muse to study and maintain the code, don't hesitate to contact us 🙂

## get asusctl-gex (inactive, awaiting updates)

You can get the current version of [asusctl-gex at extensions.gnome.org](https://extensions.gnome.org/extension/4320/asusctl-gex/)!

If you want to contribute, be sure to checkout our [Gitlab repo](https://gitlab.com/asus-linux/asusctl-gex).


## Icons/Screenshots

_The screenshots below are just examples and might not represent the current used icons._

### Screenshot

![screenshot.png](https://gitlab.com/asus-linux/asusctl-gex/-/raw/main/screenshots/screenshot.png)

**battery charge limit notification:**

![notification.png](https://gitlab.com/asus-linux/asusctl-gex/-/raw/main/screenshots/notification.png)

---

## Requirements

* gnome >= 3.36.0
* [asusctl](https://gitlab.com/asus-linux/asusctl) >= 4.0

---

## Build Instructions

### Dependencies

* nodejs >= 14.0.0
* npm >= 6.14.0

### Building (production)

In a terminal enter the following commands as a user (**do NOT run as root or sudo**):

```bash
git clone https://gitlab.com/asus-linux/asusctl-gex.git /tmp/asusctl-gex && cd /tmp/asusctl-gex
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

`cd` into the directory where you've downloaded the `asusctl-gex` source code and enter the following commands:

```bash
npm install
npm run debug
```

---

## License & Trademarks

**License:** Mozilla Public License Version 2.0 (MPL-2)

**Trademarks:** ASUS and ROG Trademark is either a US registered trademark or trademark of ASUSTeK Computer Inc. in the United States and/or other countries.
Reference to any ASUS products, services, processes, or other information and/or use of ASUS Trademarks does not constitute or imply endorsement, sponsorship, or recommendation thereof by ASUS.
The use of ROG and ASUS trademarks within this website and associated tools and libraries is only to provide a recognisable identifier to users to enable them to associate that these tools will work with ASUS ROG laptops.