# NISFETCH 3.9.0

### Fast system information. Clean visuals. Deep customization.

**NISFETCH** is a lightweight system-information fetcher written in native C11 for Linux and Android/Termux. It displays your operating system, hardware, memory, packages, display and other details beside a built-in Linux logo or your own PNG image.

It began as a personal terminal project and evolved from the streamlined **2.8.7** release into a configurable, cross-distribution tool with **163 registered modules** in **3.9.0**.

> **Inspiration:** NISFETCH was inspired by the **idea and visual concept** of [Fastfetch](https://github.com/fastfetch-cli/fastfetch). **No Fastfetch source code was used to write NISFETCH.** They are independent projects.

## Highlights

- **163 registered modules**, with adaptive Linux, KDE and Termux defaults.
- **Seven presets:** `auto`, `compact`, `balanced`, `hardware`, `network`, `phone`, `full`.
- **Native Linux probes** for CPU, GPU, RAM, filesystems, monitor EDID and refresh rate where accessible.
- **Custom PNG logos** alongside system information, with terminal-aware rendering and safe fallbacks.
- **Seven themes**, adjustable brightness, custom RGB colors, labels, module order, spacing and palette placement.
- **JSON output** for scripts, plus `--profile` for performance investigation.
- **Arch package fast/full modes** with optional verified persistent Pacman indexes.
- **Android/Termux awareness:** Android identity, phone-appropriate defaults, optional battery data and safe probe timeouts.
- **Privacy-friendly default:** the displayed local IP is `1337.1337.1337` until you explicitly request network-address modules.
- **No root required for normal use.** Missing system information is omitted or shown as unavailable rather than invented.

## Version comparison: 2.8.7 vs 3.0.0 vs 3.9.0

| Feature | **2.8.7** | **3.0.0** | **3.9.0** |
|:---|:---|:---|:---|
| Position in project | Early optimized baseline | First stable universal release | Current portability/stability release |
| Registered modules | Original core set | **146** | **163** |
| Linux distributions | Primarily Linux desktop probes | Native pacman, dpkg, apk and other package-manager paths | Same, with additional distro detection and failure handling |
| Android / Termux | Initial support; desktop fields could appear on phones | Adaptive phone output and Android-specific modules | Safer Android property collection, optional battery fallback and fewer irrelevant GPU probes |
| Logos | Built-in Tux and distribution art | Refined Tux, distro logos, custom PNG | Preserved PNG support, cleaner fallback and configurable presentation |
| Animated GIF | Not a core feature | Experimental terminal-specific support | Still experimental; **not guaranteed** on Konsole or every terminal |
| Display information | Resolution and display server | Better desktop/display detection | **Monitor brand/model, Hz, connector, size and monitor count** where exposed |
| RAM information | Used / total / percentage | Improved memory and hardware modules | Optional **DDR type, MT/s, DIMM slots, manufacturer and part number** when readable |
| Disk information | Capacity and usage | Additional disk/filesystem diagnostics | Filesystem type in default disk lines, mount/device and inode modules |
| Configuration | Basic module selection and presentation | JSONC and CLI overrides | **7 presets, themes, RGB overrides, brightness, label/spacing controls, JSON output** |
| Pacman package count | Fast cache, slower uncached foreign count | Verified persistent foreign-package index | Two persistent indexes plus selectable **`fast` / `full`** modes |
| Cold package path | External tooling could dominate startup | Direct libalpm and indexed warm path | Native archive readers, bounded streaming fallback and optimized directory scans |
| Installation | Local build/install | Backup and atomic replacement | Same safety, with more actionable distro-specific dependency errors |
| Regression testing | Earlier development tests | Expanded platform/config testing | **92 automated tests passed** in the 3.9.0 development environment |

**Scope:** The 2.8.7 module count was not independently preserved, so this table does not invent one. The 163 modules in 3.9.0 are *registered names*, not a promise that every Linux distribution or Android device exposes all 163 values.

## What changed from 3.0.0 to 3.9.0

The items below describe released functionality and subsequent hotfixes in each version family. Multiple patches deliberately kept the same displayed version.

### 3.0.0 — Universal foundation

- Reworked platform detection for Arch and other Linux desktops, including KDE Plasma/KWin, GNOME/Mutter, Xfce, Sway, Hyprland and other window managers.
- Adapted the default module list for Termux: Linux desktop, compositor and GPU details are not forced into the normal phone output.
- Improved Android version, SDK, manufacturer, model, brand, device, SoC and security-patch detection where the phone permits it.
- Added more native package collection: pacman, dpkg/Termux, apk, Portage, and command-based RPM, XBPS and pkg support.
- Expanded the registered module catalog to **146**.
- Improved PNG rendering, terminal output, prompt positioning, redirected/plain-text output and configuration precedence.
- Made cache writes atomic and installation recoverable with automatic backups of the previous executable.
- **3.0.0 index hotfix:** introduced a persistent, fingerprint-validated `pacman-foreign.index`. Foreign/AUR package classification is reused while the underlying Pacman metadata remains unchanged; database updates invalidate it.

### 3.1.0 — Monitor details

- Added monitor **manufacturer/model** detection from validated EDID exposed through Linux DRM.
- Added the active **refresh rate (Hz)** from supported display services; an EDID-preferred rate is explicitly marked as preferred rather than misrepresented as active.
- Introduced five modules: `monitor`, `refreshrate`, `displayconnector`, `displaysize`, `monitorcount`.
- Added `--detailed` for richer CPU, GPU, monitor, memory, package, disk and power information without making ordinary output unnecessarily long.
- Increased the registered module count to **151**.

### 3.2.0 — Deeper hardware information

- Extended the standard `Memory` field with the detected memory generation, for example **`3.81 GiB / 31.26 GiB (12%) | DDR4`**.
- Added optional RAM details from readable EDAC/SMBIOS data: frequency, occupied slots, total slots, module manufacturer and part number.
- Supported a clearly identified platform-based DDR estimate for specific unambiguous CPUs when direct RAM data is unavailable. Unknown values remain unknown.
- Added filesystem types to default disk entries, for example **`[ext4]`**, using the actual mount for `/` or `/home`.
- Added twelve modules: `ramtype`, `ramfrequency`, `ramslots`, `rammodules`, `rammanufacturer`, `rampart`, `diskdevice`, `homedevice`, `homefilesystem`, `diskmountopts`, `diskinodes`, `homeinodes`.
- Reached **163 registered modules**.

### 3.3.0 — Flexible configuration

- Introduced a brighter high-contrast default: warm accent labels and clearer white values.
- Added `vivid`, `soft` and `mono` themes, along with RGB color controls.
- Allowed per-module label and value colors, custom module names, explicit module ordering and hidden modules.
- Added controls for label width, spacing, palette indent, emphasis and visibility.
- Added `--init-config`, `--config-path` and `--check-config`, including validation and safe creation without overwriting a user config.
- Preserved adaptive defaults when no custom module list is pinned.

### 3.4.0 — Theme consistency and brightness

- Fixed the username/hostname header so it follows the selected theme instead of retaining an unrelated color.
- Added separate title color overrides with predictable configuration precedence.
- Expanded themes to **`vivid`, `soft`, `mono`, `ice`, `forest`, `rose`, `classic`**.
- Added brightness control via **`--theme-brightness 20..180`** or JSONC `display.themeBrightness`; `100` keeps the original theme brightness.
- Strengthened ANSI color, title, validation and error-reporting regression tests.

### 3.5.0 — Presets and machine-readable output

- Added seven layout presets: `auto`, `compact`, `balanced`, `hardware`, `network`, `phone`, `full`.
- Made preset/config/CLI override priority explicit and predictable.
- Added structured `--json` / `--format json` output with canonical module IDs and safe plain-text values.
- Kept `--profile` diagnostics on **stderr** so stdout remains usable by scripts.
- Increased validation coverage for invalid presets, colors, formats and module lists.

### 3.6.0 — Optimized core

- Kept the existing verified foreign-package index and introduced an independent `pacman-total.index`.
- Added index validation against Pacman configuration and local/sync database metadata.
- Reduced the internal standard-output buffer from **256 KiB to 32 KiB** and removed large temporary module-sorting arrays.
- Shared a single `/proc/loadavg` snapshot across related load/process modules and one `/proc/meminfo` snapshot across memory modules.
- Preserved the normal text layout, module order, JSON mode and customization.

### 3.7.0 — Native Pacman cold path

- Added direct reading of Pacman sync-database archives through runtime-loaded **libarchive** when the verified foreign index is absent or stale.
- Used an exact-name lookup structure for installed packages instead of constructing full package-manager repository objects.
- Preserved libalpm and `pacman` fallbacks for unusual configurations or archive formats.
- Tightened index invalidation to prevent stale counts from surviving rapid metadata changes.

### 3.8.0 — Package modes and micro-optimizations

- Added **`--packages-mode fast`** to count all installed Pacman packages without calculating the foreign/AUR split.
- Retained **`--packages-mode full`** as the default when the separate native/foreign classification is required.
- Kept separate short-lived cache keys so switching modes does not mix the two meanings.
- Added a bounded-memory gzip/zstd streaming reader as a fallback for Pacman repository archives, with validation and safer fallback behavior.
- Optimized Pacman local-package directory traversal with Linux `getdents64`, falling back to portable `readdir` as required.
- Reduced CPU-frequency collection overhead with direct sysfs reads and preserved the correct maximum-clock logic.
- Continued sharing memory snapshots and skipping unnecessary work on small module selections.
- **Hotfixes retained the version 3.8.0.** The fast and full package paths intentionally have different costs; full unindexed classification is not advertised as a 2 ms operation.

### 3.9.0 — Distribution and Android stability

- Read `/etc/os-release` and, if necessary, `/usr/lib/os-release`, with sane `PRETTY_NAME` → `NAME` → `ID` fallback for minimal distros and containers.
- Improved optional Termux battery reporting through `termux-battery-status`: direct process execution without shell/grep/sed, bounded waiting and validated percentage parsing.
- Prevented non-Bionic Android `getprop` collection from deadlocking when the property table becomes full.
- Avoided inappropriate desktop GPU probing in Termux without an X11/Wayland display.
- Expanded cross-platform regression fixtures for Debian/dpkg, Alpine/apk, RPM, os-release variants, Android properties, Termux battery and timeout handling.
- Kept **all 163 modules**, the seven presets, themes, PNG rendering, JSON output, full/fast package counting and both verified Pacman indexes.
- Passed **92 automated tests** and a warning-clean C11 build in the development environment.

## Installation

### Arch Linux / Manjaro / EndeavourOS

```bash
sudo pacman -S --needed base-devel libpng giflib zlib unzip

cd ~/Загрузки
unzip NISFETCH-v3.9.0-universal-stability.zip
cd NISFETCH-v3.9.0-universal-stability
sh install.sh
hash -r
nisfetch --version
```

### Debian / Ubuntu

```bash
sudo apt update
sudo apt install build-essential libpng-dev libgif-dev zlib1g-dev unzip

unzip NISFETCH-v3.9.0-universal-stability.zip
cd NISFETCH-v3.9.0-universal-stability
sh install.sh
nisfetch --version
```

### Fedora

```bash
sudo dnf install gcc libpng-devel giflib-devel zlib-devel unzip

unzip NISFETCH-v3.9.0-universal-stability.zip
cd NISFETCH-v3.9.0-universal-stability
sh install.sh
nisfetch --version
```

### Alpine Linux

```sh
sudo apk add build-base libpng-dev giflib-dev zlib-dev unzip

unzip NISFETCH-v3.9.0-universal-stability.zip
cd NISFETCH-v3.9.0-universal-stability
sh install.sh
nisfetch --version
```

### Android / Termux

```bash
pkg update
pkg install clang libpng giflib zlib unzip
termux-setup-storage

cd ~/storage/downloads
unzip NISFETCH-v3.9.0-universal-stability.zip
cd NISFETCH-v3.9.0-universal-stability
sh install.sh
nisfetch --version
```

Run `termux-setup-storage` only if you want access to Android's shared Downloads directory; otherwise transfer or extract the ZIP somewhere under your Termux home. Battery details may additionally require the optional **Termux:API** package and Android companion app.

The installer compiles NISFETCH, preserves the previous executable under a unique backup filename, and atomically installs the new one. The normal Linux destination is `~/.local/bin/nisfetch`; in Termux it is `$PREFIX/bin/nisfetch`. The script offers dependency hints when required development files are missing.

## Usage

```bash
nisfetch
nisfetch --version
nisfetch --help
nisfetch --list-modules
nisfetch --list-logos
nisfetch --list-themes
nisfetch --list-presets
```

### Modules and detail

```bash
nisfetch --no-logo --modules os:kernel:cpu:gpu:memory:packages
nisfetch --no-logo --modules display:monitor:refreshrate:displayconnector:displaysize:monitorcount
nisfetch --no-logo --modules memory:ramtype:ramfrequency:rammodules:rammanufacturer:disk:homefilesystem
nisfetch --preset hardware --detailed
nisfetch --preset network
nisfetch --preset phone
```

The default KDE desktop layout requests **29 entries** (including its title/separator); a phone or another desktop may show fewer. Optional modules do not fabricate unavailable hardware values. Some diagnostic modules can reveal identifiers or actual network addresses, so review output before posting screenshots.

### Themes, layout and JSON

```bash
nisfetch --theme ice --theme-brightness 125
nisfetch --theme forest --label-width 18 --palette-indent 14
nisfetch --theme mono --no-palette
nisfetch --preset compact
nisfetch --json --no-config --modules os:cpu:memory:packages
nisfetch --profile
```

Initialize the JSONC configuration:

```bash
nisfetch --init-config
nisfetch --config-path
nisfetch --check-config
```

Example `~/.config/nisfetch/config.jsonc`:

```jsonc
{
  "preset": "balanced",
  "packagesMode": "fast",
  "display": {
    "theme": "ice",
    "themeBrightness": 120,
    "valueColor": "#ffffff",
    "labelWidth": 17,
    "paletteIndent": 14,
    "format": "text"
  },
  "moduleLabels": {
    "cpu": "Processor",
    "gpu": "Graphics",
    "memory": "RAM"
  },
  "hiddenModules": []
}
```

CLI flags override the matching saved settings. Use `--no-config` to ignore them for a run.

### Custom PNG images

```bash
nisfetch --image '/home/nisve/Загрузки/Telegram Desktop/1789823484807.png'
nisfetch --image '/path/to/logo.png' --image-backend ansi
```

Kitty and Konsole have native graphics paths where supported; other terminals can use an ANSI half-block fallback. Native PNG transport retains the original file bytes, but on-screen quality and scrolling depend on terminal graphics support. Animated GIF behavior remains **experimental**; there is no promise of identical animation or scrollback behavior across shells and terminals.

## Package counting and performance

Arch users can choose between two distinct workloads:

| Mode | Command | Meaning |
|:---|:---|:---|
| **Full** (default) | `nisfetch --packages-mode full` | Separates repository/native and foreign/AUR packages; uses verified persistent indexes when valid |
| **Fast** | `nisfetch --packages-mode fast` | Counts all installed Pacman packages; does **not** separately classify foreign packages |

The durable indexes live under `$XDG_STATE_HOME/nisfetch/` or, if unset, `~/.local/state/nisfetch/`. Temporary caches live under `$XDG_CACHE_HOME/nisfetch/` or `~/.cache/nisfetch/`. Clearing a temporary cache does **not** erase the durable indices.

### Fair six-module warm benchmark

```bash
hyperfine -N --warmup 50 --runs 500 \
  "$HOME/.local/bin/nisfetch --no-config --no-logo --packages-mode fast --modules os:kernel:cpu:gpu:memory:packages" \
  "fastfetch --logo none --structure os:kernel:cpu:gpu:memory:packages"
```

### Cold application-cache benchmark, keeping real indexes untouched

```bash
export NIS_BENCH_STATE="$(mktemp -d)"
export NIS_BENCH_CACHE="$(mktemp -d)"

hyperfine -N --warmup 0 --runs 100 \
  --prepare 'rm -f "$NIS_BENCH_CACHE/nisfetch/packages_fast" "$NIS_BENCH_CACHE/nisfetch/gpu"' \
  "env XDG_STATE_HOME=$NIS_BENCH_STATE XDG_CACHE_HOME=$NIS_BENCH_CACHE $HOME/.local/bin/nisfetch --no-config --no-logo --packages-mode fast --modules os:kernel:cpu:gpu:memory:packages" \
  "env XDG_STATE_HOME=$NIS_BENCH_STATE XDG_CACHE_HOME=$NIS_BENCH_CACHE fastfetch --logo none --structure os:kernel:cpu:gpu:memory:packages"
```

The test above clears selected application cache entries, **not** the kernel page cache. It measures Pacman *total-package counting* through `fast` mode, not an unindexed native/foreign classification. Hardware, filesystem state, security policy, GPU driver, selected modules and caches all affect the timing.

**Historical context (not a 3.9.0 claim):** on the project's Arch/Ryzen 7 5700X system, a 3.0.0 six-module run measured approximately **1.4 ms / 5.1 MiB warm**, and approximately **2.2 ms with the disposable cache cleared but the persistent index available**. The 3.8.0 fast-path development run reported about **2.5 ms** under its cache-cleared conditions. **3.9.0 has not yet been benchmarked on that target Arch machine**, so its exact timing and the current 2.5 ms cold-start goal remain to be verified.

## Compatibility and reliability

- **Supported design targets:** Linux desktops and minimal installations; Android through Termux.
- **Package detection paths:** Pacman, dpkg, apk, Portage, RPM, XBPS and `pkg` where their databases/tools are available.
- **Display and monitor details:** require accessible display APIs or EDID. A preferred EDID rate is not mislabeled as an active rate.
- **DDR frequency and manufacturer:** require readable hardware information; root-only `dmidecode` is not silently invoked.
- **Termux battery:** optional Termux:API fallback can time out gracefully instead of hanging a shell.
- **Testing:** 3.9.0 passed 92 automated tests, including simulated cross-distro and Termux scenarios. This does **not** mean every distro release, phone, terminal emulator or display server has been tested on real hardware.
- **Performance:** small selections can be very fast, but full unindexed package classification and external display services can take longer.

## Privacy

The default `Local IP` line intentionally displays **`1337.1337.1337`** for screenshot privacy. Use `nisfetch --modules ipv4:interfaces --no-logo` to request actual network details. Device IDs, boot IDs, user paths and other sensitive modules are optional; avoid sharing `--all` output publicly without reviewing it.

## License and credits

NISFETCH is distributed under the **MIT License**. See [LICENSE](LICENSE). Distribution names and logos may be trademarks of their respective owners.

NISFETCH was inspired by the concept of [Fastfetch](https://github.com/fastfetch-cli/fastfetch), but its implementation was written independently and **does not incorporate Fastfetch source code**.

**Made by Nisve**
