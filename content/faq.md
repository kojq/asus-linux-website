+++
title = "Frequently Asked Questions"
template = "page/default.html"
+++

# asusctl

**Is `<distro>` supported by asusctl?**

Only Fedora (current version) and OpenSUSE Tumbleweed officially. Other distros may have
packaging, or you will need to compile and install it yourself.

#### It's not working!

Check the logs with `sudo journalctl -b -u asusd` and look for errors.

#### There's an error like:

 ````
 Error: D-Bus error: The name org.asuslinux.Daemon was not provided by any .service files (org.freedesktop.DBus.Error.ServiceUnknown)
 ````
  when I run `asusctl <command>`

The daemon isn't running, check the logs with `sudo journalctl -b -u asusd` and look for errors.

#### Error is something about a keyboard?

Please ensure you are using a recent kernel. As of now the minimum is 5.11, but please use at least 5.13 so that you get all the most recent patches and fixes for ASUS laptops.

#### I don't have profile or charge control

See above. It's also possible that your laptop doesn't support this so if the kernel update doesn't solve this feel free to make a :sadface: (sorry).

#### How do I set a custom fan curve?

Custom fan curves (not speaking of the built in power profiles) are currently only supported on Ryzen ROG laptops.<br>
The necessary kernel patches are merged since 5.17.

The format is shown [here](https://github.com/cronosun/atrofac/blob/master/ADVANCED.md#limits).

There are three fan profiles namely Quiet, Balanced and Performance to choose from. Each profile is linked to power profile and gets applied when the power profile is set. You can enable/disable the fan profiles using the following command:
```
asusctl fan-curve -m <profile_name> -e true/false
```
All three fan profiles can be activated at once. If no profile is activated manually then the fan curve from the BIOS is used.

To change the fan curve data for a specific profile use the following command:
```
asusctl fan-curve -m <profile_name> -D <fan_curve_data>
```

#### FN+F5 doesn't do anything?

You need to map the key-combo to an action in your desktop, like this:

![](/images/fan-shortcut.png)

# Graphics and Switching

#### Why did nvidia mode give me black screen with xorg?

This happens on AMD machines typically, if your machine is AMD then you need to
add the following to run on graphical login:

```
xrandr --setprovideroutputsource modesetting NVIDIA-0
xrandr --auto
```

there are many ways to do this so you will need to search for the right way for
your login manager.

**KDE/SDM NOTES** The location of the startup script for KDE/SSM is wrong on most resources found on the internet: To get the login screen to show up on the laptop screen when in Nvidia mode, the xrandr commands must be added to /etc/sddm/Xsetup (NOT /usr/share/sddm/scripts/Xsetup which most resources mention). (thanks `motoridersd `)

#### Switching to integrated/hybrid/nvidia  doesn't seem to work

There's a few steps to troubleshoot.

1. If you are dual-booting Windows 10 / 11 (or think Windows may have been in iGPU Mode in Armoury Crate) then try this,

`journalctl -b -u supergfxd`

If there are lines which end with `ERROR: Could not find dGPU`, then reboot into Windows and make sure that Armoury Crate has the dGPU set to Auto Mode.  If you have set it to iGPU only mode, then the dGPU will be forcibly removed from the system and Linux will no longer be able to see it.

If you no longer have Windows installed, and you suspect that it was set to iGPU only mode when you installed Linux, then you can try to forcibly enable the dGPU with the following.

```
echo 0 | sudo tee /sys/devices/platform/asus-nb-wmi/dgpu_disable
echo 1 > sudo /sys/bus/pci/rescan
echo 0 | sudo tee /sys/devices/platform/asus-nb-wmi/dgpu_disable
```

It may take a few tries to enable it, and a reboot is recommended afterwards.

2. Are your Nvidia drivers installed correctly?

In hybrid or nvidia mode they should be loaded, check with `lsmod |grep nvidia` for similar to:
```
[luke@datatron]$ lsmod |grep nvidia
nvidia_drm             65536  11
nvidia_modeset       1232896  18 nvidia_drm
nvidia_uvm           1150976  0
nvidia              34185216  906 nvidia_uvm,nvidia_modeset
drm_kms_helper        274432  2 nvidia_drm,i915
drm                   618496  14 drm_kms_helper,nvidia_drm,i915
```
if that doesn't show similar, then you need to reinstal your Nvidia drivers.

3. Is Nouveau blocklisted correctly? It's common to require the following in `/etc/default/grub`:
```
GRUB_CMDLINE_LINUX="rd.driver.blacklist=nouveau modprobe.blacklist=nouveau"
```
that line may not be exact, and *do not* delete existing entry to match it, just add those two items to the end of the line before the last "

4. What does `journalctl -b -u asusd` say after a failed switch?

If it contains lines like the following then we need to look at active sessions
```
asusd[1373]: INFO: GFX: Switching gfx mode to integrated
asusd[1373]: INFO: GFX: display-manager thread started
asusd[1373]: WARN: GFX: thread time exceeded 3 minutes, exiting
```
Do: `loginctl list-sessions`, if it shows more than one session lets have a look with:
```
loginctl show-session --property Type --property Class --property Active <num>
```
where `<num>` is the number or char+num from the `SESSION` column of the output above.
If there is more than one `active` session of type `Wayland | X11 | Mir` then somehow
you've ended up with an extra session.

Save your work, `Ctrl+Alt+F4` (or any F`<num>`) key to a TTY terminal session and run
```
asusctl graphics -m <desired mode>
sudo loginctl kill-user <username>
```
your display-manager should then restart.

5. If none of the above helps, and/or you use KDE, and/or have a G14 with GTX
graphics, you may need to this,
```
sudo sed -i 's/#KillUserProcesses=no/KillUserProcesses=yes/' /etc/systemd/logind.conf
```
then `sudo systemctl restart display-manager`.

### Dynamic Boost on AMD CPUs
As of version 510.60.02, NVIDIA still doesn't support dynamic boost on AMD CPUs. This is a known issue might get fixed in some future release. For now you can disable the powerd service:

```bash
systemctl disable nvidia-powerd.service
```

# Keyboard

#### My keyboard is not working with the driver?

You may have a different keyboard. Please request support in one of the
related projects on gitlab, or in the discord server.

#### Mic-Mute doesn't work?

The issue is that the current keycode being emitted by the driver is only
recognised by Wayland. X11 and desktops using X11 require `F20` to be emitted.

Create a file named `/etc/udev/hwdb.d/90-nkey.hwdb` with:

```
# Format evdev:input:b<bus_id>v<vendor_id>p<product_id>

# ** Note **
# The line evdev:input:b0003v0B05p1866* may vary on your ASUS Laptop.  
# Modify the <vendor_id> and <product_id> based on the output of this command to ensure remaps work:
# $ lsusb | grep 'ASUSTek Computer, Inc. N-KEY Device' | awk -F'[: ]' '{print $7" "$8}' | tr '[:lower:]' '[:upper:]'

evdev:input:b0003v0B05p1866*
  KEYBOARD_KEY_ff31007c=f20 # x11 mic-mute
```

then update hwdb with:

```
sudo systemd-hwdb update
sudo udevadm trigger
```

#### Can I customise the `Fn` key?

No, the key is on a physically different circuit and used to *physically*
signal the keyboard EC to switch key circuits. There are three different
circuits for the `0x8166` keyboard.

#### I have a G14/G15 or other laptop where the Arrow keys are unmarked by emit keycodes, can I use these?

Yes

#### Can I remap the arrow-keys?

A1: Yes, create a file named `/etc/udev/hwdb.d/90-nkey.hwdb` with:

```
# Format evdev:input:b<bus_id>v<vendor_id>p<product_id>

# ** Note **
# The line evdev:input:b0003v0B05p1866* may vary on your ASUS Laptop.  
# Modify the <vendor_id> and <product_id> based on the output of this command to ensure remaps work:
# $ lsusb | grep 'ASUSTek Computer, Inc. N-KEY Device' | awk -F'[: ]' '{print $7" "$8}' | tr '[:lower:]' '[:upper:]'

evdev:input:b0003v0B05p1866*
  KEYBOARD_KEY_c00b6=kbdillumdown # Fn+F2 (music prev)
  KEYBOARD_KEY_c00b5=kbdillumup   # Fn+F4 (music skip)
  KEYBOARD_KEY_ff3100c5=pagedown  # Fn+Down
  KEYBOARD_KEY_ff3100c4=pageup    # Fn+Up
  KEYBOARD_KEY_ff3100b2=home      # Fn+Left
  KEYBOARD_KEY_ff3100b3=end       # Fn+Right
```

then update hwdb with:

```
sudo systemd-hwdb update
sudo udevadm trigger
```

You can see a list of keycodes [here](https://github.com/torvalds/linux/blob/b76f733c3ff83089cf1e3f9ae233533649f999b3/include/uapi/linux/input-event-codes.h).

#### I have an ASUS laptop without the PrtSc/SysRq key. Can I remap any key to SysRq?

Yes! Similar to remapping the Arrow-Keys above, you can remap - say the `menu (fn+RightCtrl)` key to `SysRq`.  
Just add another line to `/etc/udev/hwdb.d/90-nkey.hwdb` with the following, including the leading whitespaces:
```
  KEYBOARD_KEY_<ScanCode>=sysrq        # force remap sysrq to Fn+RightCtrl
```
You can get the `<ScanCode>` by running
```
evtest /dev/input/by-id/usb-ASUSTeK_Computer_Inc._N-KEY_Device-*-kbd
```
and pressing the `RightCtrl` key.  
In this case, it is `70065`
```
Testing ... (interrupt to exit)
Event: time 1662839073.640933, type 4 (EV_MSC), code 4 (MSC_SCAN), value 70065 <--------- Substitute this as <ScanCode>
Event: time 1662839073.640933, type 1 (EV_KEY), code 127 (KEY_COMPOSE), value 1
Event: time 1662839073.640933, -------------- SYN_REPORT ------------
```
Then update hwdb with:

```
sudo systemd-hwdb update
sudo udevadm trigger
```
#### Where are the DKMS modules

A: Archived. All patches that were used to make those modules are now upstreamed. For the best ASUS experience please ensure you are using the minimum of 5.13 kernel.

# Desktop Notifications

#### How do I get feedback for Fn+Key presses?

asusctl comes with a notification daemon and service you can enable:

```
systemctl --user enable asus-notify.service
systemctl --user start asus-notify.service
```

this works fine for almost all desktops and provides basic notifications through
dbus.

There is also:
- [Gnome extension](https://gitlab.com/asus-linux/asus-nb-gex)


## S3 (legacy suspend) for the 2021 / 2022 G14 and G15

Things could get unstable in terms of suspending the system from time to time (depending on the kernel version).

Usually those are issues with the newer suspend methods, called s0ix.

You could patch your DSDT tables so your machine uses the older suspend method, called S3.

In our tests this works great on the 2021 / 2022 G14 and G15.

Those patches are not part of the main repo and can't be. It will always be a manual matter and can't get integrated into the kernel.

**Important:** If you update the BIOS be sure you **disable** your DSDT table and **create a new** one. DSDT tables could change with newer BIOS versions!

You can find the script here:

https://gitlab.com/marcaux/g14-2021-s3-dsdt


## 2021 G15 secondary NVMe suspend patch

If you have a 2021 G15 and use a second NVMe drive you might experience a problem while using s0ix (s2idle) resulting in a delayed system suspend.

The problem lies within a faulty DSDT table and the secondary NVMe drive does not enter suspend properly.

With an updated DSDT table you can apply the necessary patches to make it suspend properly.

https://gitlab.com/smbruce/GA503QR-StorageD3Enable-DSDT-Patch

**Important:** This is not necessary if you want to use S3 (see above). It applies only if you want to further use the standard suspend method s0ix.


## Dual booting with Windows

Be sure you think about the following when dual booting with Windows:

- disable fast boot within the BIOS
- disable fast boot in Windows
- always fully shutdown after using and switchting to another OS so the hardware gets correctly initialized

If you still experience an issue hold the power button while on battery for a few seconds to force a shutdown. This often helps to reset some things.
Those steps are not needed when you run Linux exclusively.

# 3rd Party Tools

Other tools are available:
- [asusctltray, tray icon tool](https://github.com/Baldomo/asusctltray) GTK extension.
- [supergfxctl-plasmoid](https://gitlab.com/Jhyub/supergfxctl-plasmoid) For KDE.
- [asusctl-argos](https://github.com/vijay-prema/asusctl-argos) For Gnome.
- [supergfxctl-waybar-module](https://github.com/The0919/supergfxctl-waybar-module)  For Sway / waybar.
- <del>[asus-x13-gpu-switching](https://github.com/hyphone/asus-x13-gpu-switching) Alternative to `supergfxctl` for Asus ROG Flow X13 2021 *only*</del> No longer needed, see below.
- <del>[asusctltray-alt](https://github.com/m0nk3h/asusctltray-alt) GTK extension for asus-x13-gpu-switching</del> No longer needed, see below.

# AniMe Matrix Display

 - [ZephyrusBling](https://github.com/Meumeu/ZephyrusBling)

# Note for Asus ROG Flow X13 (2021) users

The BIOS versions 408 & 409 cannot boot a Linux kernel newer than 5.15.x so you will need to upgrade to the 410 bios [here](https://rog.asus.com/laptops/rog-flow/2021-rog-flow-x13-series/helpdesk_bios)

<del>The 407 & 410 BIOS have a bug which will fail to turn off the dGPU if you switch to `integrated` mode using Supergfxctl.  In `integrated` mode, `supergfxctl -S` should show `off`, but in this case the dGPU will fail to turn off and will actually consume a fair amount of power and raise your temperatures by ~10c.  Using `hybrid` mode will leave your dGPU `suspended` until you launch a program which is configured to use the dGPU and is the recommended solution.</del>

<del>There is an experimental package [asus-x13-gpu-switching](https://github.com/hyphone/asus-x13-gpu-switching) which will allow you to switch between `integrated` and `hybrid` modes which will successfully power off the dGPU in `integrated` mode.  It is *not* compatible with `supergfxctl`, so as noted in the "How to use" section, you will need to uninstall `supergfxctl` to try this method.</del>

Supergfxctl v5.0.0 and above now supports switching between Hybrid, Integrated and Vfio modes on the Flow X13.

{{
  section_contribute(
    head="Do you find this resource helpful?"
    text=true
    donate=true
  )
}}
