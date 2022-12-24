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
