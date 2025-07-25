<img src="https://github.com/filotimo-project/branding/blob/main/banner-lightbackground-whitebackground.jpg?raw=true" alt="filotimo" width="450"/>

## [Download an ISO here](https://download.filotimoproject.org/filotimo-latest.iso), and [the checksum here](https://download.filotimoproject.org/filotimo-latest.iso-CHECKSUM)

## This is a work in progress.

Where are the packages? https://copr.fedorainfracloud.org/coprs/tduck973564/filotimo-packages/

Where is the image built? https://github.com/filotimo-project/filotimo

Where can I download an ISO? [Here.](https://download.filotimoproject.org/filotimo-latest.iso)

How can I contribute? Contact @fesdonomist, or simply submit a pull request :)

## What's improved from normal Fedora Atomic

filotimo aims to provide a system that just works out of the box for most people, and when this is not possible, provide accessible, obvious and easy GUI based tools to get things to work.

This list does not include most of the improvements that Universal Blue provides. To learn about those, see [their site](https://universal-blue.org/). Filotimo is based on their base images.

This list also will not be comprehensive, but will be expanded as time goes on. Only notable things are included. To understand the full extent of the changes, the source code is the best guide.

### Preinstalled software
#### Generally, you can expect much better multimedia and hardware compatibility with Filotimo compared to normal Fedora.
- The limited Fedora flatpak remotes are removed and replaced with the much better Flathub.
- Homebrew is preinstalled. This allows easy installation of terminal utilities without needing a distrobox container.
- AppImageLauncher is preinstalled. This allows easy integration of AppImages with the system.
- `kio-onedrive` is preinstalled for easy access to OneDrive cloud storage through Dolphin and KDE's inbuilt Online Accounts integration.
- A variety of useful KDE utilities are preinstalled as Flatpaks.
- Various codecs are installed, including libheif, x265, and hardware decode support for some non-free codecs. This allows video to simply work, which it does not on stock Fedora.
- VA-API and libva drivers are preinstalled, allowing for hardware acceleration out-of-the-box, improving battery life.
- Extra printer drivers, firmware, a multitude of kernel modules, udev rules, the bazzite kernel and, optionally, NVIDIA drivers are preinstalled, improving compatibility with a wide range of hardware including and not limited to DisplayLink docks, Xbox controllers, Razer hardware, RGB hardware, ThinkPads, Mediatek WiFi adapters and much more.
- Virt-manager, podman, docker, and distrobox are preinstalled for easy virtualisation and container workflows.
- Virt-manager is set up to work properly with SELinux.
- Samba and kdenetwork-filesharing are included and preconfigured for filesharing over a local network. This is usually quite annoying to configure, but will now just work through Dolphin. A patched kdenetwork-filesharing is installed with much better UX.
- firewall-config is preinstalled for easy UI-based firewall configuration and has a shortcut in System Settings. 
- supergfxctl is included on NVIDIA images to control hybrid graphics systems.
- `fish` is installed as the default shell. Don't worry! It's wrapped with a script so `/etc/profile` still works.
- Fcitx is installed and configured out of the box for easy use of IME and other keyboard layouts.
- Some nicer and more professional-looking backgrounds are included with the system and the default background is changed.
- Microsoft fonts (Calibri, Arial, etc) are preinstalled.
- [Atychia](https://github.com/filotimo-project/atychia) is preinstalled, which is an in-house solution that allows you to recover your system if something goes awry or easily restart Plasmashell with a keyboard shortcut (Meta+Ctrl+Shift+B), rather than having to resort to an unfamiliar TTY.
- There's a convenient shortcut in Kickoff that allows you to restart to your Windows installation, assuming you have one installed.
- Furthermore, GRUB is hidden by default for a clean bootup experience, but re-enables itself if you force shutdown your system in case you need to rollback your system image, or if you're restarting from a rollback deployment.
- You get all the relatively up-to-date packages and new technologies that Fedora with KDE provides. Expect better Wayland support and support for things like fractional scaling, variable refresh rate, HDR, and so on (an X11 session is not preinstalled and use of one is not supported by this project).

## Changes at the system level
- `sudo` inherits locale settings and QT/KDE environment variables.
- Fonts are reconfigured as such: the default serif typeface is IBM Plex Serif, the default sans-serif typeface is Inter, and the default monospace typeface is IBM Plex Mono.
- `vm.max_map_count` is increased to `2147483642`, mainly improving compatibility with some Windows games.
- `zram` is configured differently to stock Fedora, with the following options set:
```
vm.swappiness = 180
vm.watermark_boost_factor = 0
vm.watermark_scale_factor = 125
vm.page-cluster = 0
vm.vfs_cache_pressure=66
```
, improving performance with zram, which is configured [to 2 times the system memory amount](https://issuetracker.google.com/issues/227605780). This should significantly improve responsiveness on memory-constrained systems, and even slightly improve things on normal systems. zram is a much more desirable and much faster alternative to disk-based swap, which other distros such as Mint use.
See https://github.com/ublue-os/bazzite/issues/1570 and https://github.com/pop-os/default-settings/pull/163
- `zram` automatically uses `zstd` instead of the default `lzo-rle` on systems with 16GiB of RAM or below, significantly increasing the compression ratio, albeit incurring a less significant performance penalty.
- Dynamic rebalancing of BTRFS file systems is enabled by default.
- Users can mount drives without authentication (through graphical interfaces), which also fixes the KDE automounter.
- The bazzite kernel is preinstalled, which ships with much better hardware compatibility and increased performance.
- The BORE scheduler is used by default - as per Bazzite and CachyOS, improving responsiveness under load and improving gaming performance.
- Users are automatically appended to `adbusers` group to ensure compatibility with Android udev rules.
- Users are automatically appended to `plugdev` group to ensure compatibility with Yubikey udev rules.
- Open file and memory lock limits are increased for compatibility with certain software and emulators.
- `inotify` limits are increased.
- The following are set for improved networking performance:
```
net.core.default_qdisc=fq
net.core.netdev_max_backlog=16384
net.core.somaxconn=8192
net.ipv4.tcp_congestion_control=bbr
net.ipv4.tcp_fastopen=3
```
- The following are set for improved kernel hardening:
```
kernel.kexec_load_disabled=1
kernel.kptr_restrict=1
```
- Dirty centisec values are dynamically determined for storage hardware.
- IO schedulers are dynamically determined - using Kyber for fast SSDs and BFQ for rotational devices.
- minimum-free, dirty ratio and bytes, dirty background ratio and bytes, and zram compression algorithm are dynamically determined.
- Full preempt is enabled by default - this reduces raw throughput but improves latency and responsiveness on the desktop.
- The following are applied to the system's default btrfs subvolumes in fstab, improving disk performance and reducing unnecessary writes - `noatime,lazytime,commit=120,discard=async,compress=zstd:1,space_cache=v2`
- Some kernel module fixes are automatically applied for Surface and Framework devices.
- `s2idle` sleep is used by default for systems with the NVIDIA driver and hardware.

## Changes affecting KDE
- This ships with some changes to the Breeze theme:
    - The dark theme uses a more neutral, darker and more appealing color palette compared to Breeze Dark (which has been [upstreamed as of 6.4](https://invent.kde.org/plasma/breeze/-/merge_requests/506)).
    - The default panel has been made slightly taller to improve padding.
    - The accent colour is a darker and deeper blue compared to Breeze.
    - The default font for the UI is Inter.
    - The default monospace font is IBM Plex Mono.
    - Kickoff uses a list instead of a grid on the Favourites tab.
    - Kickoff no longer has action button captions enabled.
    - Anticipating changes to Kickoff in 6.3, category switching on hover has been explicitly enabled.
    - The Dolphin icon has been set back to the generic file manager one, removing the actual dolphin that was added.
    - Discover has a customised, filotimo-branded icon.
- Discover is configured to automatically update your system daily.
- Spectacle automatically copies your screenshots to the clipboard.
- KWrite immediately opens to a new document instead of the welcome view.
- Text files open in KWrite by default, rather than Kate.
- KRunner uses Google by default.
- Plasma automatically starts with an empty session, instead of the somewhat broken saved session functionality.
- KRunner floats in the middle of the screen instead of being at the top.
- `kdesu` uses sudo by default instead of su, since Fedora and by extension Filotimo locks the root account.
- The automounter is fixed and enabled by default, and is configured to automatically mount all devices that were manually mounted once. This saves you from having to mess with fstab or the Partition Manager, potentially breaking your system.
- Konsole is shipped with a nicer profile by default.
- Some new icons are shipped for supergfxctl and generic icons are used for Fcitx, improving their visual integration with the rest of the system.
- Some convenience scripts are included with `ujust`, including:
    - The commands built in to all ublue images - including one to quickly set up Davinci Resolve
    - One which sets up a KDE development environment in a distrobox - even allowing you to log into it from SDDM!
- LibreOffice document templates are included in `~/Templates`.
- Some nicer wallpapers are included.
- The SDDM background can be set, which isn't possible on base Kinoite.
- Accent colours are properly applied to Flatpaks which use Breeze-GTK.
- Dolphin has Git integration enabled by default.
- Dark titlebars are used by default for Discord, Vesktop and Spotify.
- Some shortcuts in System Settings are added for these apps: `firewall-config`, AppImageLauncherSettings, Kleopatra, `kjournald`, and `systemdgenie`.
- `QT_SCALE_FACTOR_ROUNDING_POLICY` is set to `Round` which fixes blurriness with fractional scaling.
- The KDE Store is nuked out of orbit, with the Discover backend for it deleted and the popups for it disabled globally in Plasma. This is done because it floods Discover with a bunch of mostly low-res, botted wallpaper submissions and a bunch of broken themes - [many of which execute arbitrary code that can destroy your system and delete all your files.](https://blog.davidedmundson.co.uk/blog/kde-store-content/)
- Plasma Vaults is removed. It's really broken and the UX sucks.

### Changes to Firefox
#### UX
- Firefox will use the KDE file picker by default instead of the GTK one.
- Firefox will ask where to save each file by default.

#### Performance (not a comprehensive list)
- Prefetching is enabled by default for reduced slowness.
- The minimum threshold for low memory is raised to 750MB of memory free.
- Browser tabs will unload themselves when memory is constrained.
- WebRender is enabled by default.
- Certain network IO options with quite antiquated defaults are fixed, improving browsing performance.

#### Privacy and security
- The vast majority of Mozilla telemetry is disabled.
- Tracking protection is set up with more strict options by default out of the box.
- HTTPS-only mode is enabled by default.
- Global Privacy Control is enabled by default.
- Trending search suggestions, Firefox Relay and Pocket are disabled by default.
- Suggestion and extension recommendations while you browse are disabled by default.
- Fingerprinting protection and Global Privacy Control are enabled by default.

#### Multimedia
- Widevine is enabled by default, allowing use of DRM protected sites like Netflix.
- The OpenH264 plugin is enabled by default, so video playback should work in all cases and video conferencing should just work on sites like Cisco WebEx.
- VA-API is enabled by default, enabling hardware decode in more situations. This should improve battery life.

## What's planned
This is only a few rough ideas of what we want to include.

- Graphical utilities to see and change the SELinux state and configure Secure Boot easily.
- Switcheroo integration - SUPERGFX is pretty horrible.
- A storage page in System Settings to easily clear wasted disk space and view storage status.
- An alert system that notifies you if there's hard disk corruption or a disk is about to fail, if Secure Boot is misconfigured, if you'd be better served by the NVIDIA or another image for hardware compatibility, etc...
- A library for Qt/C++ that abstracts over bootc (and other things), asynchronously with QCoro/C++20 coroutines, which these utilities can be built upon.
- A settings page for (rpm-)ostree, which allows easy configuration of various options and allows easy rebasing to different Filotimo images, as well as allowing you to adjust some system level settings (such as hostname and environment vars) with some YaST-esque sysadmin tools that are unified under a System Administration KCM.
- A centralised Software Updates app, which updates brew, distroboxes and the system all at once. Also include things like CVE warnings, changelogs, and an auto-updater daemon. Discover's rpm-ostree integration is not very good, and not very fixable.
- A graphical utility to run Windows apps in a Docker container and integrate them with the rest of the system through FreeRDP - something like WinApps but more elegant
- ~~A small utility to prompt the user to either look for a Linux alternative or install Bottles or create a VM (link to documentation) when they attempt to open an .exe as well as for .deb or .rpm packages~~ -- DONE: https://filotimoproject.org/appcompatibilityhelper
- Waydroid integration.
- A Kirigami GUI to manage distroboxes and containers.
- Proper Konsole integration with container-based workflows, like Ptyxis.
- A nice website and hosting solution -- SEE: https://filotimoproject.org/
- Hook into `systemd` to set up a swapfile immediately before hibernating to make this work properly without having to have a swapfile all the time, see: https://gitlab.com/evlaV/steamos-customizations/-/commit/3d13dcb03258d237fc0a8dc9d3d74a1da7fe7385
