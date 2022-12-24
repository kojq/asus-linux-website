## TODO

- Determine whether to use markdown vs HTML
- Determine layout
- Determine directory structure

- Include patches and dGPU behavior
- Render code blocks with  sh <(curl link) for a given use case
- Remove outdated instructions
- Make light and dark theme consistent

- Q&A
  - We only support distributions with systemd init
  - We only support packages listed on this site
    - To clarify, for typical use, we do not support compiling packages or packages maintained by others such as those on the AUR
    - If you require an unsupported distribution without multi-booting or virtualization, consider using container technology such as with distrobox (https://github.com/89luca89/distrobox).
  - The below recommendations will apply to *most* derivative distributions as well
    - Popular distributions that are recommended or supported (parse values like 37 from site)
      - Fedora 37 Workstation (https://getfedora.org/en/workstation/download/)
      - Fedora Silverblue 37 (https://silverblue.fedoraproject.org/download)
      - Fedora SPINS (https://spins.fedoraproject.org/)
      - Arch Linux (https://archlinux.org/download/)
      - openSUSE Tumbleweed (https://get.opensuse.org/tumbleweed/)
      - openSUSE MicroOS (https://get.opensuse.org/microos/)
    - Popular distributions that are not recommended or supported
      - Manjaro
      - Ubuntu
      - Pop!_OS
  - asusctl/supergfxctl instructions
  - removing redundant information
  - clarifying technical aspects
  - use battop to determine power draw
  - Embed content
    - https://edgeup.asus.com/2022/flow-vs-strix-vs-zephyrus-vs-tuf-whats-the-difference-between-asus-gaming-laptops/
    - https://www.asus.com/us/Laptops/For-Gaming/All-series/filter?Series=ROG-Republic-of-Gamers&SubSeries=ROG-Zephyrus,ROG-Flow,ROG-Strix
      - Discord change name to include this information and include Linux distribution being used
    - seasonal theming of the site would be nice from a design standpoint (dunno, maybe a little CSS based on the date).


## Welcome

ASUS Linux is an independent community effort that works to improve Linux support for ASUS laptops.

Many ASUS ROG laptops work quite well under Linux. However, there are always new models coming out or old models that were overlooked. Our project aims to enable ROG-specific features and solve issues you may have.



Sound issues are the most common problem , and solving these requires a little more effort and patience - see the blog post about this plus visit the discord for guidance. Do note that the root problem is almost never pulse audio or pipewire - it's the Realtek codec chip needing specific fixes in the kernel driver.

The second most common issue is that of device feature enablement such as:

    AniMe Matrix display
    RGB keyboard LEDs
    fn+key combos and media controls
    G-Sync enable on capable laptops
    Re-enable the dGPU after setting "iGPU On" in Windows
    and more All of the above is either enabled in asusctl already, or is enabled via patches to the kernel (and we provide pre-built kernels with the patches applied) which are steadily upstreamed.

Extra features we currently support are:

    Control graphics modes via supergfxctl without reboots for switching between iGPU, dGPU, on-demand, and vfio (for VM pass-through)
    Set battery charge limit
    (custom) fan curve control



Seperate page

Want to contribute?

If this site helps you and you want to contribute we welcome you to help with development, search for issues, join the discord, and add to this website.
You can also donate

Your support on Pateron or Paypal helps us buy ASUS devices for development to support new features and drivers. 
Discord, Gitlab, Patreon, PayPal

Privacy Policy
