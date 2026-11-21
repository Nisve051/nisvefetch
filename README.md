# NISFETCH 3.0.0

**Fast system information. Clean visuals. Linux and Android/Termux support.**

NISFETCH is a lightweight, native C system-information utility. It displays system details next to a large Tux, a distribution logo, or your own PNG image. The information panel is designed to remain readable, the shell prompt stays **below** the fetch output, and regular terminal scrolling continues to work.

This README documents the **3.0.0 persistent-index build**, including the evolution from **2.8.7 through 3.0.0**, the final features, compatibility notes, module inventory, installation, and real benchmark results.

> **Project origin:** NISFETCH was inspired by the *idea* of [Fastfetch](https://github.com/fastfetch-cli/fastfetch)—a beautiful and fast system-information display. **Fastfetch source code was not used to write NISFETCH.** Comparisons below are benchmark results from one test machine, not a claim that one tool is universally faster.

## At a glance

| Feature | NISFETCH 3.0.0 |
| --- | --- |
| Implementation | Native C11 |
| Registered modules | **146** |
| Standard KDE desktop preset | **29 requested entries**, including title/separator; unavailable fields can be omitted |
| Logo choices | **21**: Tux plus 20 named distribution/platform logos |
| Custom artwork | PNG; GIF decoding and experimental terminal-specific animation |
| Environments | Linux desktops, TTY, Android through Termux |
| Configuration | JSONC-compatible config and CLI overrides |
| Privacy default | Screenshot-safe `Local IP: 1337.1337.1337` |
| License | MIT |

## Everything that changed since 2.8.7

### 1. Visual design and layout

- The visual direction evolved into **Clean Modern**: larger, more recognizable artwork; clearer contours; gentler distribution-specific colors; and stronger contrast between labels and values.
- Tux received multiple visual passes to improve its silhouette, remove unwanted light pixels at transparent edges, reduce the rough pixelated look, and avoid blurry enlargement. Its source canvas is **58 × 72 RGBA pixels**; the terminal rendering is scaled conservatively.
- Built-in distribution artwork was refined with alpha transparency, so supported logos do not appear on a white rectangle. The project includes polished colors for NixOS blue, Manjaro green, openSUSE, Void, Android, and other designs.
- Large built-in artwork uses a shared proportional layout: approximately **43% of available terminal width** is targeted for the logo, leaving the majority of the width for text. The actual size also respects the terminal dimensions and minimum text space.
- Logos and information are aligned horizontally. Long values are clipped to terminal-cell width without splitting UTF-8 characters.
- **CPU Cores** and **Processes** are placed directly below **CPU** in the default layout; **Load (1m)** is moved to the very bottom after the rest of the default information. User-defined module order takes priority.
- The palette is spaced away from the artwork. The information panel uses bold warm-colored labels and bright white values by default. A two-row color palette appears only when the terminal has room.
- The header separator follows the visible username/hostname width. The program adds spacing **after** the full fetch, leaving the next shell prompt below the artwork rather than above or inside it.
- Small terminals have adaptive behavior: if a large logo would squeeze the text too much, it is reduced or hidden. Unavailable system-specific fields do not need to clutter the default output.

### 2. Built-in logos

Current names accepted by `--logo`:

```text
tux (default)
arch          debian        ubuntu        fedora        manjaro
linuxmint     opensuse      gentoo        alpine        nixos
android       termux        void          artix         endeavouros
kali          raspbian      slackware     redhat        popos
```

There are **15 built-in RGBA pixel-art variants**: `arch`, `debian`, `fedora`, `popos`, `kali`, `linuxmint`, `redhat`, `gentoo`, `opensuse`, `slackware`, `alpine`, `void`, `manjaro`, `nixos`, and `android`.

Other named logos currently use a compact text-art fallback. Not all 20 distribution/platform logos are supplied as high-resolution pixel artwork. Use `nisfetch --list-logos` to see the exact list in your installed build.

### 3. PNG images, GIF experiments, and terminal behavior

- Added `--image '/absolute/path/file.png'` to replace Tux with a user image **without removing system information**.
- Added terminal-aware PNG output, with native PNG transport in compatible **Kitty** and **Konsole** sessions and a safe ANSI truecolor half-block fallback elsewhere.
- The native PNG path sends the **original PNG file bytes**, avoiding an unnecessary decode/re-encode cycle. Display quality still depends on terminal scaling, font-cell geometry, and graphics support.
- Transparent PNG pixels use the terminal background rather than a forced white box. Fallback rendering is necessarily limited by terminal-cell resolution.
- Added `--image-backend auto|kitty|konsole|ansi` for selecting or overriding the backend.
- Added bounded image handling (maximum **2048 × 2048** pixels), PNG validation, and fallback decoding.
- GIF handling went through several experimental patches: looping playback, Kitty-native graphics, scroll-linked Unicode image placeholders, rendering/scrollback fixes, and separate renderer prototypes that were later integrated or abandoned.
- The stable goal throughout these iterations was preserved: the **prompt must remain usable below the fetch** and ordinary commands must continue to scroll normally.
- **Honest limitation:** animated GIF rendering is **experimental**. It depends on terminal features and is **not guaranteed in Konsole, Termux, or every terminal emulator**. A static frame or an ANSI fallback is expected when animation cannot be provided safely. PNG is the recommended stable custom-image format.
- No separate `nisfetch-image` command is required for the current integrated PNG feature.

### 4. More modules and more accurate information

- Expanded the module system to **146 registered names**. The 2.9.x series added extensive optional diagnostics for CPU, memory, storage, network, OS, sessions, processes, time, hardware, and Android.
- The **3.0.0 source contains 16 module names not in the preserved 2.9.8 module list**:

  ```text
  gpuusage        gpuvram         gpumemoryused    cpugovernor
  cpufreqmin      cpufreqmax      memcached       batteryhealth
  androidbrand    androidhardware gpudriver        gpuvendor
  cpul3           memorypressure  batterycycles    biosdate
  ```

- Improved optional modules for CPU cores, frequency, load, process totals, GPUs, memory, disks, filesystems, network interfaces, IPv4/IPv6, battery, charging, hardware identity, and Android properties.
- Added/fixed friendly values such as **chassis type** instead of a bare DMI number, **Rolling Release** on Arch when appropriate, and proper `1 hour` / `2 hours` grammar (and `1 min` / `2 mins`).
- Default desktop output includes CPU core count, process count, and one-minute load, with the last entry appearing at the bottom.
- Modules unavailable on a given host may be skipped in adaptive presets; explicitly requested modules remain available where supported.
- `--list-modules` lists registered module names, **not** a guarantee that all 146 will return nonempty information on every system.

### 5. Android and Termux adaptation

- Android/Termux became a supported target rather than only an Android logo.
- The default phone preset **does not attempt to show desktop-only details** such as a Linux window manager, KDE themes, desktop icons, display manager details, or a PC GPU entry.
- Android identity modules cover version, SDK level, manufacturer, brand, model, SoC, device, board, hardware, build fingerprint, and security patch where Android exposes that data.
- Phone-oriented optional details include battery level/health/cycles, brightness, timezone, locale, and Termux paths. Restricted battery properties may require **Termux:API** and its companion Android app.
- Android property discovery uses Android/Bionic native property APIs when available, with a batched `getprop` fallback instead of repeatedly starting one command per property.
- Termux packages are counted from the native `dpkg` status file when possible. The installer targets `$PREFIX/bin/nisfetch` rather than assuming desktop Linux paths.
- Termux behavior is feature-adaptive; specific device permissions and manufacturer restrictions still matter.

### 6. Linux distribution, desktop, and shell support

- Broader detection for KDE Plasma/KWin, GNOME/Mutter, Cinnamon/Muffin, MATE/Marco, Xfce/Xfwm4, Hyprland, Sway, niri, i3, COSMIC, labwc, river, and Wayfire.
- Improved desktop-specific settings detection, including KDE themes, icons, fonts, cursor themes and window-manager theme, while omitting inappropriate KDE-only fields on other default desktop presets.
- More careful Wayland/X11 display detection; a missing Wayland monitor probe is not silently replaced with an unrelated XWayland virtual size.
- Terminal detection uses available environment and process information rather than treating the generic `TERM` value as a reliable terminal-emulator name.
- Native or optimized package detection paths for **pacman, dpkg, apk, Gentoo Portage**, plus one-process fallback queries for **RPM, XBPS, and pkg** families. User and system **Flatpak** apps are counted separately.
- Clean non-interactive output: redirecting to a file or pipe suppresses unnecessary color codes, huge logos, and palettes by default. The `--no-color` flag also works interactively.
- These are supported detection paths, not a claim of successful testing on every Linux distribution, shell, compositor, Android device, or terminal version.

### 7. Performance: warm, temporary-cache-cleared, and truly unindexed

- Collection work is performed only for requested modules where practical, helping small module selections finish quickly.
- GPU, display and desktop probes were profiled separately. Unnecessary shell pipelines were reduced, repeated configuration values were cached, and fast direct reads from `/proc`, `/sys`, and package-manager databases were added.
- A prior optimization reduced the warm default fetch from roughly **15–16 ms** to around **1.5–1.9 ms** in the user's test environment, with memory usage falling from about **22 MiB to 4.8 MiB**. These were development measurements and not a standardized cross-version comparison.
- The most expensive remaining **first-run** package path was identified: `pacman -Qmq` could take about **149 ms**. Calling `libalpm` directly avoided the subprocess but still required expensive repository metadata reading when fully uncached.
- NISFETCH 3.0.0 introduced a **verified persistent Pacman foreign-package index** at `~/.local/state/nisfetch/pacman-foreign.index` (or under `XDG_STATE_HOME`). It records the foreign package count and a fingerprint derived from `pacman.conf`, local database metadata, and enabled sync database metadata.
- The installer prepares this index once. Regular runs validate it cheaply. Pacman DB changes invalidate it so the AUR/foreign count is recomputed rather than blindly reused.
- The index is written using a temporary file and atomic rename; regular ephemeral cache entries are also written safely. This protects against partially written values when multiple terminals start together.
- **Important:** clearing `~/.cache/nisfetch` does **not** clear the persistent index. True first-time indexing, or rebuilding after Pacman database changes, can still be much slower. The index improves repeated and disposable-cache-cleared launches; it does not make a complete uncached repository scan magically free.

## Development patch history

This is a feature-level history reconstructed from the preserved development releases, build files, and test outputs. Multiple hotfixes retained the same version number, so this groups them by their **public version family**, not by a made-up semantic version for each ZIP.

| Version / patch family | Changes delivered or explored |
| --- | --- |
| **2.8.7** | Starting point for this comparison; the English uptime singular/plural fix (`1 hour`, `2 hours`) was already present in this development line. |
| **2.8.8** | Android-adaptive defaults, especially omitting PC-only desktop/GPU information in Termux. |
| **2.9.0 — Clean Modern** | Unified cleaner visual direction for the default Tux and distribution logos; preservation of distinctive distro colors and transparency. |
| **2.9.1** | Large modular expansion with optional hardware, OS, process, memory, network, Android, and environment diagnostics; CPU cores/processes/load entered the expanded default desktop preset. |
| **2.9.2** | Follow-up module/value correctness and visual polish after testing the expanded module set, including better chassis/OS-version presentation and adaptable layout. |
| **2.9.3** | Speed and memory work, default layout balancing, a larger smoother Tux, and graphics refinement. Profiling exposed the expensive truly cold path despite excellent warm results. |
| **2.9.4–2.9.6** | Further Tux sharpness, removal of stray edge pixels, brighter text, logo/text geometry, CPU-adjacent core/process fields, phone modules, palette spacing, and bottom-positioned load line/prompt. |
| **2.9.7** | Custom image support began: PNG and GIF paths, compositing beside ordinary system information, and groundwork for terminal-dependent rendering. |
| **2.9.8 (multiple hotfixes)** | Integrated image commands; PNG quality and compatibility improvements; GIF looping experiments; Kitty graphics/placeholders; Konsole artifact avoidance; shell-interaction, cursor and scrollback fixes; safe fallbacks. Experiments that could not be made portable were **not** promoted to a universal GIF guarantee. |
| **3.0.0 — Universal Stability** | More robust Linux/Termux behavior, desktop detection, native package counting, safer cache, safe installation/backups, improved CLI precedence, 16 additional registered modules, and focused PNG stability. |
| **3.0.0 — Pacman fastpath / persistent-index hotfixes** | In-process libalpm foreign-package classification, followed by the persistent validated index that removes the repeated expensive classification cost on unchanged Arch installations. **Version remains 3.0.0.** |

## Real benchmarks: NISFETCH vs Fastfetch

Measured on the developer's **Arch Linux / Ryzen 7 5700X / KDE** machine with `hyperfine`; six selected modules and no logo for each tool. Values are means over the indicated runs.

| Scenario | Runs | NISFETCH 3.0.0 | Fastfetch | Result |
| --- | ---: | ---: | ---: | --- |
| Warm run, 50 warmups | 500 | **1.4 ± 0.4 ms** | 2.5 ± 0.1 ms | **~1.8× faster** in this test |
| Memory, same warm test | 500 | **5.1 MiB** | 6.4 MiB | **~20% less memory** |
| Temporary cache cleared each run; persistent index kept | 100 | **2.2 ± 0.3 ms** | 2.6 ± 0.2 ms | **~15% faster** in this test |
| Memory, cache-cleared test | 100 | **5.1 MiB** | 6.3 MiB | **~19% less memory** |

Commands used:

```bash
# Warm:
hyperfine -N --warmup 50 --runs 500 \
  "$HOME/.local/bin/nisfetch --no-config --no-logo --modules os:kernel:cpu:gpu:memory:packages" \
  "fastfetch --logo none --structure os:kernel:cpu:gpu:memory:packages"

# Clear disposable caches but retain the installed Pacman index:
hyperfine -N --warmup 0 --runs 100 \
  --prepare 'rm -rf "$HOME/.cache/nisfetch" "$HOME/.cache/fastfetch"' \
  "$HOME/.local/bin/nisfetch --no-config --no-logo --modules os:kernel:cpu:gpu:memory:packages" \
  "fastfetch --logo none --structure os:kernel:cpu:gpu:memory:packages"
```

Package classification was independently verified:

```text
NISFETCH: 1329 (pacman), 20 (AUR/foreign), 3 (flatpak)
pacman -Qmq | wc -l
20
```

**Benchmark scope:** these results are hardware-, configuration-, module-, and cache-dependent. In an earlier fully unindexed test, NISFETCH took **~149.5 ms** while Fastfetch took **~2.5 ms**. Full libalpm recomputation can likewise take much longer than the cached result. The persistent index was developed specifically to reduce the common-case cost while keeping the foreign count verifiable.

## Installation

### Arch Linux / EndeavourOS / Manjaro

Download and extract the current `NISFETCH-v3.0.0-persistent-index.zip` release:

```bash
sudo pacman -S --needed gcc libpng giflib zlib unzip
cd ~/Загрузки
unzip NISFETCH-v3.0.0-persistent-index.zip
cd NISFETCH-v3.0.0-persistent-index
sh install.sh
nisfetch --version
```

The installer builds in a temporary directory, installs to `~/.local/bin/nisfetch`, and keeps the previous binary in a **uniquely named backup** such as `~/.local/bin/nisfetch.backup.XXXXXXXX`. On Arch-family systems it also prepares the Pacman foreign-package index once.

### Android / Termux

```bash
termux-setup-storage
pkg update
pkg install clang libpng giflib zlib unzip
cd ~/storage/downloads
unzip NISFETCH-v3.0.0-persistent-index.zip
cd NISFETCH-v3.0.0-persistent-index
sh install.sh
nisfetch --version
```

The installer chooses `$PREFIX/bin/nisfetch` inside Termux. Android property and battery details depend on device permissions and available Android/Termux APIs; root is not required for basic fetch functionality.

### Other Linux distributions

Install a C compiler plus development packages for **libpng, giflib, zlib**, and the normal platform linker/libc. Then run `sh install.sh`. Package names vary between distributions, and some optional probes require platform-specific utilities.

## Usage

```bash
nisfetch                         # Default adaptive summary and built-in logo
nisfetch --version
nisfetch --help
nisfetch --list-logos
nisfetch --logo arch
nisfetch --logo android
nisfetch --no-logo
nisfetch --no-config             # Ignore ~/.config/nisfetch/config.jsonc
nisfetch --no-color              # Plain text on a TTY
nisfetch --profile               # Setup / collect / render timings on stderr
nisfetch --list-modules
nisfetch --all                   # All registered modules; can expose private data
nisfetch --modules os:kernel:cpu:cpucores:processes:gpu:memory:packages
```

### Use your own PNG instead of Tux

```bash
nisfetch --image '/home/nisve/Загрузки/Telegram Desktop/1789823484807.png'

# Force a terminal backend if detection is unsuitable:
nisfetch --image '/home/nisve/Загрузки/Telegram Desktop/1789823484807.png' --image-backend konsole
nisfetch --image '/home/nisve/Загрузки/Telegram Desktop/1789823484807.png' --image-backend ansi
```

### GIF: experimental

```bash
nisfetch --image '/home/nisve/Загрузки/tenor(2).gif'
nisfetch --image '/home/nisve/Загрузки/tenor(2).gif' --animate-cycles 0
nisfetch --image '/home/nisve/Загрузки/tenor(2).gif' --no-animate
```

Zero cycles requests an endless animation where the supported experimental backend permits it. **This is not a cross-terminal promise**: use PNG for stable results, especially in Konsole and Termux.

## Configuration

NISFETCH reads `${XDG_CONFIG_HOME:-$HOME/.config}/nisfetch/config.jsonc` when present. No config file is needed for the built-in defaults. The `--gen-config` command **prints a sample**; redirect it to create a file.

```bash
mkdir -p "$HOME/.config/nisfetch"
nisfetch --gen-config > "$HOME/.config/nisfetch/config.jsonc"
nisfetch --check-config
```

Example `config.jsonc`:

```jsonc
{
  "logo": {
    "type": "tux",
    "image": "",
    "enabled": true,
    "autoScale": true,
    "width": 0
  },
  "display": {
    "gap": 2,
    "palette": true,
    "keyColor": "#ffd36e",
    "valueColor": "#ffffff"
  }
}
```

Command-line `--image` and `--logo` override the corresponding saved choice; `--no-logo` avoids image decoding. To load a custom config, use `--config /path/to/config.jsonc`. Configurations from the 2.9.x line remain compatible with the 3.0.0 format.

## All 146 registered modules

The complete installed inventory comes from `nisfetch --list-modules`. Some names overlap by design (for example `load1` versus `loadavg`, or `memory` versus `memused`). A listed name means the module is registered; availability and the meaning of the result depend on the OS and hardware.

<details>
<summary>Show the complete 146-module list</summary>

```text
title
separator
os
host
kernel
uptime
packages
display
desktop
wm
wmtheme
theme
plasmatheme
icons
font
cursor
session
terminal
cpu
gpu
memory
swap
disk
homedisk
localip
locale
board
bios
chassis
shell
processes
loadavg
load1
load5
load15
datetime
timezone
hostname
architecture
cpucores
cpufreq
battery
brightness
bluetooth
network
diskusage
diskfilesystem
termux
androidversion
androidsdk
androidmanufacturer
androidmodel
androidsoc
bootid
virtualization
editor
users
language
distro
osversion
kernelversion
kernelarch
machineid
cpuvendor
cpumodel
cpubogomips
cpuload
memtotal
memavailable
swaptotal
swapfree
diskfree
disktotal
diskpercent
rootfs
mounts
interfaces
ipv4
ipv6
gateway
dns
username
uid
gid
home
path
shellversion
terminaltype
displayserver
sessiondesktop
xdgconfig
xdgcache
xdgdata
pid
parentpid
threads
systemd
init
cgroup
entropy
thermalzones
fans
batterycount
powerstate
androiddevice
androidboard
androidfingerprint
androidsecuritypatch
termuxprefix
termuxhome
timezoneoffset
epoch
week
dayofyear
bootdate
procversion
filesystems
environment
cpuonline
cpuconfigured
procsrunning
procstotal
memused
mempercent
diskavailable
uptimeseconds
bootepoch
loadtasks
kernelhostname
systemhz
gpuusage
gpuvram
gpumemoryused
cpugovernor
cpufreqmin
cpufreqmax
memcached
batteryhealth
androidbrand
androidhardware
gpudriver
gpuvendor
cpul3
memorypressure
batterycycles
biosdate
```

</details>

## Pacman index details

On Arch-family systems, the foreign-package count has two paths:

1. **Prepared / unchanged repository:** verify the compact persistent index and use the stored foreign count. This avoids repeatedly parsing sync repository databases.
2. **Missing/stale index:** recalculate the foreign/native classification using `libalpm` where possible; fall back to `pacman` if required, then rewrite the index. This initial step may take substantially longer.

The index is stored in `${XDG_STATE_HOME:-$HOME/.local/state}/nisfetch/pacman-foreign.index`. It is independent of the temporary `~/.cache/nisfetch` directory and is invalidated when tracked Pacman database metadata changes. Index validation is a performance optimization, **not** an absolute guarantee that filesystem metadata cannot be manipulated externally.

```bash
# Validate displayed foreign-package count:
nisfetch --no-config --no-logo --modules packages
pacman -Qmq | wc -l

# Optional per-stage profiling:
nisfetch --no-config --no-logo --modules packages --profile
```

## Privacy and security

- **`1337.1337.1337` is intentional:** the default `Local IP` line is a privacy-friendly screenshot placeholder, not a valid network address.
- Use `--modules ipv4:interfaces` when you actually want to see local interface details.
- Optional fields such as `machineid`, `bootid`, `androidfingerprint`, `path`, `ipv4`, and `ipv6` may reveal identifying information. Avoid `--all` in publicly shared screenshots.
- Ordinary fetch usage does not need root privileges.
- Animated graphics support and external terminal utilities are subject to the behavior and permissions of the host environment.

## Tests and project status

The source archive includes unit/regression tests for configuration, package counting, cache behavior, platform adaptation, rendering, and mocked libalpm/index scenarios.

```bash
# Requires Python and, for graphical tests, Pillow:
python -m unittest discover -s tests -v

# Inspect registered modules and available logos:
nisfetch --list-modules | wc -l
nisfetch --list-logos
```

Stable core features: C executable, text-mode system information, built-in logos, custom PNG, normal shell prompt and scrollback, adaptive desktop/phone defaults, warm performance, and persistent Arch package indexing.

Experimental or device-dependent features: terminal-native GIF animation, some GPU sensors, advanced battery diagnostics, specific compositor integration, and uncommon package manager configurations.

## License and credits

NISFETCH is released under the [MIT License](LICENSE). Copyright © 2026 Nisve. Distribution names, icons and associated trademarks belong to their respective owners.

**Inspiration:** [Fastfetch](https://github.com/fastfetch-cli/fastfetch). NISFETCH is an independent implementation; **no Fastfetch source code was copied into the project.**

**Made by Nisve**
