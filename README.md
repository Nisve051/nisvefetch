# NISFETCH

**System information for Linux and Android/Termux, written in C.**

NISFETCH reports hardware, operating-system and desktop information in a configurable terminal layout. It includes a large Tux logo, adaptive presets for headless systems, and optional image rendering. An experimental KDE Konsole patch supports GIF playback anchored to terminal scrollback.

### Relationship to Fastfetch

NISFETCH was inspired by the idea behind **[Fastfetch](https://github.com/fastfetch-cli/fastfetch)**, but it is an **independent implementation**. It is not a fork, modification, or rebranded build of Fastfetch. NISFETCH has its own C source code, modules, caching, rendering and hardware detection paths. Fastfetch is used as a reference point for comparing functionality and measuring performance.

**Current release:** NISFETCH **3.13.11** (`NISFETCH-3.13.11.zip`)  
**Project:** [Nisve051/nisvefetch](https://github.com/Nisve051/nisvefetch)  
**Releases:** [GitHub Releases](https://github.com/Nisve051/nisvefetch/releases) · [Beta](https://github.com/Nisve051/nisvefetch/releases/tag/beta)

> The custom `Local IP: 1337.1337.1337` value is intentional. It is part of the project's presentation and is not a failed attempt to detect a real IP address.

## Features

| Area | Information |
| --- | --- |
| Operating system | Distribution, release, architecture, kernel, init system, boot time, uptime |
| Hardware | Host model, CPU, threads, CPU frequency and temperature, GPU, driver, GPU temperature |
| Memory and storage | RAM usage and available memory, memory type when detected, swap, disk usage and filesystem |
| Desktop | Display resolution and refresh rate, desktop environment, window manager, themes, icons, font, cursor, session |
| Virtual machines | KVM/QEMU detection, hypervisor signature, virtual GPU and virtual display |
| Additional information | Installed packages, shell, terminal, process count, load averages, locale |
| Customization | 167 named modules, presets, themes, configuration, JSON output, logos, images |

NISFETCH uses hardware and operating-system interfaces when available. Missing measurements are omitted or identified as unavailable instead of being invented. For example, a virtual display's refresh rate is derived from an active DRM/KMS mode when the guest exposes it; a list of supported modes is not treated as proof of the active refresh rate.

The regular `nisfetch` command keeps the **original full-size Tux**. Custom images are opt-in.

## Changes since previous versions

The following comparison is based on the documented 3.0.0 and 3.9.0 release features and the changes introduced through 3.13.11. Version **2.8.7** is treated as an earlier 2.x baseline; its complete feature list was not available for a detailed module-by-module audit.

| Compared with | Improvements available in 3.13.11 |
| --- | --- |
| **2.8.7** | The later 3.x generation provides the documented persistent package-index architecture, expanded Linux and Termux adaptation, more detailed memory/storage information, active display detection, virtualization modules, deep profiling and image support. Exact individual features that first appeared between 2.8.7 and 3.0.0 are not attributed without the older source. |
| **3.0.0** | Preserves its native package-counting and atomic cache foundation, adding EDID monitor identification and refresh rates (3.1), RAM technology and detailed disk information (3.2), the 3.9 stability improvements, virtual GPU/KVM reporting (3.11), active DRM/KMS detection (3.12), and subsequent profiling/performance work (3.13). |
| **3.9.0** | Retains its distribution/Termux stability changes and 167-module framework, while adding better SSH/headless defaults and shell reporting (3.10), virtual GPU and hypervisor information (3.11), active virtual-display refresh rates (3.12), faster Arch desktop startup paths (3.13), and optional GIF/Konsole integration. |

### Main updates in the 3.13 series

- **Performance:** replaced several repeated subprocess and heavy initialization paths with native reads or validated indexes. Improvements cover package metadata, KDE font lookup, shell version detection and display queries.
- **Profiling:** `--profile` and `--profile-deep` show startup and per-stage timings to help identify bottlenecks.
- **Display accuracy:** reports active DRM/KMS mode and refresh rate when accessible; does not assume that supported modes are currently in use.
- **Virtual machines:** keeps GPU, display and hypervisor detection useful even in SSH sessions when the guest exposes the required interfaces.
- **GIF support:** a separately patched KDE Konsole can animate GIF frames while keeping the artwork aligned with the normal scrolling output.
- **Still images:** optional ImageMagick fallback supports additional formats such as JPEG, WebP, BMP and TIFF, subject to locally installed codecs and the current image-size limits.
- **Compatibility:** the original large Tux, existing module selection and standard no-animation use remain available.

The updated image support and experimental terminal patch are optional. The default NISFETCH invocation does not start an animation process.

## Requirements

NISFETCH compiles locally. It requires a C compiler, libpng, giflib, and zlib development files. The installation script automatically places the resulting binary in `~/.local/bin` on Linux and backs up an existing installation.

### Arch Linux

```bash
sudo pacman -S --needed base-devel libpng giflib zlib unzip
```

### Debian / Ubuntu

```bash
sudo apt update
sudo apt install build-essential libpng-dev libgif-dev zlib1g-dev unzip
```

### Fedora

```bash
sudo dnf install gcc make libpng-devel giflib-devel zlib-devel unzip
```

### Alpine Linux

```bash
sudo apk add build-base libpng-dev giflib-dev zlib-dev unzip
```

### Android / Termux

```bash
pkg update
pkg install clang libpng giflib zlib unzip
```

Termux uses its own `$PREFIX/bin` installation destination. Desktop-only probing is skipped when no graphical session is available.

### Optional image codecs

PNG and GIF are decoded natively. For other formats, NISFETCH 3.13.11 can use **ImageMagick** if it is installed:

```bash
# Arch Linux
sudo pacman -S --needed imagemagick

# Debian / Ubuntu
sudo apt install imagemagick
```

This enables JPEG/JPG, WebP, BMP, TIFF, TGA, PNM and other formats supported by your local ImageMagick installation. AVIF, HEIC and SVG depend on additional delegates, codecs, and security policies. No image decoder can guarantee every existing format. Non-GIF animated formats are currently displayed as static images.

The optional converter is only used when a custom image requires it; ordinary startup and native PNG/GIF handling do not invoke ImageMagick. Converted images are subject to a maximum decoded resolution of 2048 × 2048 pixels and resource limits. Treat complex image files from unknown sources with caution.

## Install NISFETCH

### Option A: install the release archive

1. Open [GitHub Releases](https://github.com/Nisve051/nisvefetch/releases).
2. Choose the latest release and download its **NISFETCH ZIP asset**. Do not confuse a release asset with GitHub's automatically generated source-code ZIP.
3. In the directory containing the downloaded asset, run:

```bash
unzip -o NISFETCH-3.13.11.zip
cd NISFETCH-v3.13.11-universal-adaptive
sh install.sh
export PATH="$HOME/.local/bin:$PATH"
hash -r
nisfetch --version
nisfetch
```

The command expects the release asset to be named **`NISFETCH-3.13.11.zip`** and to contain the directory `NISFETCH-v3.13.11-universal-adaptive/`. If GitHub returns `404`, check the published assets before retrying.

### Option B: install from checked-out sources

If the repository contains the NISFETCH source directory, run the installer from that directory:

```bash
cd NISFETCH-v3.13.11-universal-adaptive
sh install.sh
export PATH="$HOME/.local/bin:$PATH"
hash -r
nisfetch --version
```

`sh install.sh` is intentionally supported; no global installation and no root privileges are required for the normal Linux install. To make the user-local binary available in future Bash sessions, add `export PATH="$HOME/.local/bin:$PATH"` to your shell configuration.

## Usage

```bash
nisfetch                                  # Default Tux and system information
nisfetch --help
nisfetch --version
nisfetch --list-modules
nisfetch --list-presets
nisfetch --preset full
nisfetch --no-logo --no-color
nisfetch --modules cpu:gpu:memory:display
nisfetch --format json
nisfetch --profile-deep
```

### Custom images

```bash
nisfetch --image "$HOME/Pictures/example.png"
nisfetch --image "$HOME/Pictures/photo.jpg"
nisfetch --image "$HOME/Pictures/photo.webp"
```

The `--image` option does not change the default Tux configuration. The custom image is only used for that invocation or when explicitly selected in the user's configuration.

If an image fails to decode, inspect the file with `file` and, when available, `magick identify`. A `.png` filename does not guarantee that the file contains valid PNG data. Very large files or formats requiring missing codecs may need conversion. At present, native PNG decoding has its own size limit; ImageMagick fallback behavior may depend on the exact build.

## Animated GIFs in KDE Konsole (experimental)

NISFETCH can transmit a GIF as an iTerm2 inline image. Our **custom Konsole patch** retains the GIF data in Qt, plays its frames using `QMovie`, and keeps the image anchored to terminal scrollback. Unlike an ANSI animation loop, NISFETCH can exit and return control to your interactive shell.

The patched Konsole supports:

- GIF playback alongside NISFETCH's normal information panel.
- Normal cursor movement and terminal output without shifting the GIF downward.
- Scrollback-aware image placement: the GIF moves upward with its original lines.
- Repainting only the affected image area, rather than the whole terminal.
- Pausing animation when the image leaves the visible viewport and resuming when it returns.
- Bounded decoded-frame caching for smaller animations.

**The patch is not an official KDE feature. Stock Konsole is not expected to provide this behavior.** The patched terminal is optional, and standard NISFETCH does not require it.

### Konsole build dependencies

The patch was developed against a specific KDE Konsole development snapshot: **commit `8bc9a987e`**, reporting version **27.03.70**. KDE Frameworks 6, Qt 6, CMake, Ninja, and the relevant KDE development components must be installed.

On Arch Linux, start with:

```bash
sudo pacman -S --needed \
  git base-devel cmake ninja extra-cmake-modules \
  qt6-base qt6-multimedia qt6-imageformats \
  kconfig kcoreaddons kconfigwidgets kxmlgui \
  ki18n kio kpty knotifications knewstuff kdoctools
```

Depending on the KDE snapshot and your system, CMake may request additional Qt/KF6 development packages. Install any **explicitly reported missing dependency** before continuing. Package names and requirements evolve with KDE releases.

### Build the patched terminal

Keep the experimental build separate from the system-provided Konsole.

**1. Clone the compatible source revision:**

```bash
mkdir -p "$HOME/Projects"
git clone https://invent.kde.org/utilities/konsole.git "$HOME/Projects/konsole-gif"
cd "$HOME/Projects/konsole-gif"
git checkout 8bc9a987e
```

**2. Apply the cumulative patch.** Obtain `patches/konsole-gif-complete.patch` from this repository. From the Konsole source directory:

```bash
# Clone the NISFETCH repository if you have not done so yet.
git clone https://github.com/Nisve051/nisvefetch.git "$HOME/Projects/nisvefetch"

cd "$HOME/Projects/konsole-gif"
PATCH_FILE="$HOME/Projects/nisvefetch/patches/konsole-gif-complete.patch"

patch --dry-run -p1 < "$PATCH_FILE"
patch -p1 < "$PATCH_FILE"
```

In this example, the NISFETCH repository was cloned to `$HOME/Projects/nisvefetch`. Change `PATCH_FILE` if you placed it elsewhere. The cumulative patch contains the GIF decoder, scrollback anchoring, rendering optimization, and viewport pause behavior. **Do not apply the older individual experimental patches on top of it.**

**3. Configure and build:**

```bash
cmake -S . -B build -G Ninja -DCMAKE_BUILD_TYPE=Release
cmake --build build -j 8
```

If your source checkout already contains a correctly configured `build` directory, the build command alone is sufficient. An error such as `build is not a directory` usually means you are running CMake from the wrong working directory.

**4. Launch your custom Konsole:**

```bash
cd "$HOME/Projects/konsole-gif"
LD_LIBRARY_PATH="$PWD/build/bin${LD_LIBRARY_PATH:+:$LD_LIBRARY_PATH}" \
  ./build/bin/konsole --separate
```

The custom application and its shared libraries are loaded from the local build. Your distribution's system Konsole remains installed and available as a stable fallback.

### Create a `konsole-gif` command

After confirming the patched build works:

```bash
mkdir -p "$HOME/.local/bin"
ln -sfn "$HOME/Projects/konsole-gif/build/bin/konsole" \
  "$HOME/.local/bin/konsole-gif"

"$HOME/.local/bin/konsole-gif" --version
```

Run it using the same library environment as the test launch if your build does not have sufficient runtime search paths. A symlink alone does not guarantee that a custom Konsole will load the matching build libraries.

To assign a keyboard shortcut in KDE Plasma, open **System Settings → Keyboard → Shortcuts → Add Command**. Set the executable to the full path of `konsole-gif`, include `--separate`, and choose the desired key combination. Ensure it does not conflict with the original Konsole shortcut.

### Test the GIF with NISFETCH

Open a window of the **patched Konsole**, then execute:

```bash
nisfetch --image "$HOME/Pictures/animation.gif" --image-backend iterm
```

The `iterm` backend is specifically for this experimental GIF integration. For normal PNG and other still images, use `nisfetch --image FILE` and let NISFETCH choose its normal rendering path.

If the GIF scrolls away, playback should pause; scrolling back to its original lines should resume it. The image is tied to the terminal scrollback, not pinned to a fixed screen coordinate. Note that `nisfetch --stop-animation` manages the older ANSI worker mode, **not** `QMovie` animations owned by the patched terminal.

### GIF compatibility and performance

- This is an experimental patch that may require updating when Konsole changes its internal APIs.
- The original Tux and normal terminal behavior are unchanged when no GIF is selected.
- The cost of visible animation depends on GIF dimensions, frame rate, Qt version, and graphics driver.
- Off-screen pause reduces processing but does not necessarily release cached GIF frames still referenced by terminal history.
- Do not overwrite your distribution's Konsole binary or libraries with an untested build.
- When using SSH, image rendering is performed by the **local terminal**, even if NISFETCH runs on a remote host.

On one Arch Linux development system, reducing the repaint region lowered visible-GIF CPU usage from roughly **11.5% to 5% of one CPU**, according to `pidstat`. Those are machine-specific observations, not a universal performance guarantee.

## Benchmarking against Fastfetch

Benchmark both commands without their artwork to reduce rendering differences:

```bash
BENCH_CACHE=$(mktemp -d)

hyperfine \
  --warmup 20 \
  --runs 100 \
  --env "XDG_CACHE_HOME=$BENCH_CACHE" \
  'nisfetch --no-logo --no-color' \
  'fastfetch --logo none --pipe'
```

For a test with a fresh **application cache** before each execution:

```bash
BENCH_CACHE=$(mktemp -d)

hyperfine \
  --warmup 0 \
  --runs 100 \
  --prepare "find '$BENCH_CACHE' -mindepth 1 -maxdepth 1 -exec rm -r -- {} +" \
  --env "XDG_CACHE_HOME=$BENCH_CACHE" \
  'nisfetch --no-logo --no-color' \
  'fastfetch --logo none --pipe'
```

This is **not** a true cold kernel page-cache test. NISFETCH may store validated package indexes under `XDG_STATE_HOME`, which is not reset by these commands. For a fair comparison of detection overhead, also compare matched module sets: NISFETCH's default output may report more fields than Fastfetch.

### Measured results

NISFETCH was benchmarked against Fastfetch on an **Arch Linux desktop with an AMD Ryzen 7 5700X**. These `hyperfine` measurements are for **NISFETCH 3.13.8**, with **100 runs** and both logos disabled:

| Benchmark | NISFETCH 3.13.8 | Fastfetch | Difference |
| --- | ---: | ---: | --- |
| Warm application cache — wall time | **2.0 ± 0.2 ms** | 3.4 ± 0.1 ms | NISFETCH ~1.7× faster |
| Warm application cache — memory | **5.1 MiB** | 6.6 MiB | ~23% less memory |
| Cleared XDG cache per run — wall time | **2.9 ± 0.3 ms** | 3.4 ± 0.2 ms | NISFETCH ~1.17× faster |
| Cleared XDG cache per run — memory | **5.2 MiB** | 6.6 MiB | ~21% less memory |

Earlier 3.13 development builds measured approximately **17–36 ms** in some disposable-cache tests. Profiling identified expensive font lookup, shell subprocesses, display detection and other collection paths; subsequent optimizations reduced startup time in later builds.

Those earlier runs used different intermediate builds and cache states, so they do **not** establish a controlled version-to-version speedup. The table shows historical **3.13.8** results, not independently verified **3.13.11** results. System load, package indexes, GPU access and differing default modules can affect comparisons. GIF playback CPU usage is measured separately from NISFETCH startup time.

## Diagnostics and troubleshooting

```bash
nisfetch --version
nisfetch --no-logo --no-color --profile-deep
nisfetch --modules virtualgpu:virtualdisplay:hypervisor --no-logo
```

**Image cannot be decoded:** Check `file YOUR_IMAGE` and `magick identify YOUR_IMAGE`. Verify the file is readable, the codec is installed, and the image is within supported resource limits. Some files with `.png` names are not valid PNG files.

**GIF is static or black:** Verify you are using the patched Konsole and `--image-backend iterm`. Test the Konsole build with `LD_LIBRARY_PATH` as shown above. Some GIFs exceed the current decoder or frame-memory bounds.

**Refresh rate is unavailable in a VM:** The active DRM/KMS mode may not be accessible. A mode listed under `/sys/class/drm/card*-*/modes` is only a supported mode, not necessarily the active mode. NISFETCH avoids inventing a refresh rate.

**The system still runs an older version:** Confirm `command -v nisfetch`, `nisfetch --version`, and the precedence of `~/.local/bin` in `PATH`. Run `hash -r` after installing from a shell that previously used the older binary.

**Konsole patch fails to apply:** Verify the exact upstream commit and start from a clean checkout. The cumulative patch is based on the development snapshot, not an arbitrary current KDE branch.

## Licensing and acknowledgments

See the NISFETCH [`LICENSE`](LICENSE) file for project terms. KDE Konsole has its own licenses and upstream authors; this experiment is **not an official KDE or Fastfetch feature**.

NISFETCH is an independent project inspired by Fastfetch's approach to terminal system information. Thanks to the developers and maintainers of Linux, KDE Konsole, Qt and the libraries used by NISFETCH.

---

**Made by Nisve**
