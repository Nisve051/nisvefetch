# NISFETCH
Fast, colorful system information for Linux and Android/Termux terminals.

Version: 2.8.7
Language: C11

## ABOUT
NISFETCH is a lightweight terminal system-information tool for Linux and
Android via Termux. It features a colorful pixel-art Tux logo, distro-specific
and Android pixel logos, configurable output, and performance-focused native
system detection. Android/Termux detection is supported, although platform
coverage varies by device and available system tools.

The default layout places the logo on the left and system information on the
right. The project aims to keep rich functionality without sacrificing speed.

## INSPIRATION AND ORIGINALITY
NISFETCH was inspired by Fastfetch and the idea of displaying system
information in a terminal. Fastfetch served as inspiration for the concept,
not as a source-code base: NISFETCH was developed independently without
copying or incorporating Fastfetch source code. NISFETCH is a separate project
and is not affiliated with or endorsed by the Fastfetch project.

## FEATURES
- Pixel-art Tux logo by default, plus multiple distro and Android logos.
- Truecolor terminal output and customizable colors and spacing.
- 115 available information modules; select only what you want to display.
- System, hardware, desktop, display, package, network, and session information.
- KDE/Plasma configuration fast paths and optimized package detection.
- JSONC configuration with configurable modules and logo.
- Built-in performance profiling via --profile.
- Compiles from C11 source; no sudo required for user-local installation.

## REQUIREMENTS
- Linux (best tested on Arch Linux with KDE Plasma/Wayland).
- Android via Termux (supported in source; compatibility and available
  modules depend on Android version, permissions, and Termux packages).
- A C compiler (GCC or Clang).
- A terminal with UTF-8 and truecolor support for the intended appearance.
- Some optional modules may depend on desktop tools or platform-specific data.

## INSTALLATION
Download the NISFETCH release ZIP, extract it, and run:

```text
cd nisfetch-build
sh install.sh
```

The installer compiles the program and installs it to:

```text
~/.local/bin/nisfetch
```

No sudo is needed. Make sure ~/.local/bin is in your PATH.

Alternatively, compile manually from the source directory:

```text
cc -std=c11 -O2 -Wall -Wextra -Werror nisfetch.c -o nisfetch -lm
./nisfetch
```

## QUICK START
```text
nisfetch
nisfetch --help
nisfetch --version
nisfetch --no-logo
nisfetch --logo tux
nisfetch --list-logos
nisfetch --list-modules
nisfetch --modules os:kernel:cpu:gpu:memory:uptime
nisfetch --all
nisfetch --profile
```

## CONFIGURATION
Generate a starter configuration:

```text
mkdir -p ~/.config/nisfetch
nisfetch --gen-config > ~/.config/nisfetch/config.jsonc
```

Example JSONC:

```text
{
  "logo": {
    "type": "tux",
    "enabled": true,
    "autoScale": true,
    "width": 0
  },
  "display": {
    "gap": 2,
    "palette": true,
    "keyColor": "#ffc930",
    "valueColor": "#e4e7ec"
  },
  "modules": [
    "title", "separator", "os", "host", "kernel", "uptime",
    "packages", "display", "desktop", "wm", "wmtheme",
    "theme", "plasmatheme", "icons", "font", "cursor",
    "session", "terminal", "cpu", "gpu", "memory",
    "swap", "disk", "homedisk", "localip", "locale"
  ]
}
```

Other configuration commands:

```text
nisfetch --check-config
nisfetch --config /path/to/config.jsonc
nisfetch --no-config
```

## PERFORMANCE BENCHMARK
Measured on Arch Linux with an AMD Ryzen 7 5700X, AMD Radeon RX 6700 XT,
and KDE Plasma/Wayland using Hyperfine (1,000 measured runs per program,
50 warmup runs, -N mode):

```text
Metric                  NISFETCH v2.8.7     Fastfetch
------------------------------------------------------
Mean execution time     1.6 +/- 0.3 ms      3.4 +/- 0.2 ms
Minimum                 1.2 ms              3.1 ms
Maximum                 3.3 ms              4.1 ms
Mean memory usage       4.8 MiB             6.6 MiB
```

In this benchmark, NISFETCH was approximately 2.1 times faster and used
approximately 27% less memory than Fastfetch.

Benchmark command:

```text
hyperfine -N --warmup 50 --runs 1000 \
  "$HOME/.local/bin/nisfetch" \
  "fastfetch"
```

These are results from one machine using warm caches and each program's
current default configuration. The displayed module sets were not matched.
The results do not establish universal superiority across systems or workloads.
Cold or cache-refresh runs can be substantially slower.

For internal timing details:

```text
nisfetch --profile
```

## NOTES AND KNOWN LIMITATIONS
- Screenshot-friendly default: the 'Local IP' field displays the intentionally
  non-routable-looking text 1337.1337.1337 instead of exposing a real address.
  This is a display convention useful when sharing terminal screenshots, not
  a network security or anonymization mechanism. To inspect actual network
  information, explicitly request the ipv4 or interfaces modules, where
  supported. Note: 1337.1337.1337 is NOT a valid IPv4 address.
- Some results are cached for speed; a first or cache-refresh run can be
  significantly slower than subsequent runs.
- Hardware, desktop, and package detection vary by distribution and setup.

## PROJECT FILES
```text
nisfetch.c              Main C source
nis_tux.h               Default Tux pixel artwork
nis_distro_pixels.h     Distribution/Android pixel logos
install.sh              User-local installer
config.example.jsonc    Example configuration
```

## CONTRIBUTING
Bug reports, reproducible performance results, compatibility fixes, and
improvements to detection accuracy are welcome. Include your distribution,
desktop environment, NISFETCH version, command, and observed output.

## LICENSE
MIT License. See the accompanying LICENSE file for the full license text.
Before publishing, ensure the separate LICENSE file has the correct
copyright attribution and that all included code may be distributed under MIT.

Made by Nisve
