# HrimOS

*Between worlds, we rest.*

HrimOS is a themed KDE Plasma look-and-feel built on top of [EndeavourOS](https://endeavouros.com/), featuring an icy, cracked-ice/lightning aesthetic across the window style, decoration, colors, icons, cursors, and branding.

This repository contains the Arch Linux packages (`PKGBUILD`s) used to build and install the full HrimOS look on any Arch-based system, plus the tooling to build a full bootable HrimOS installer ISO.

---

## Table of contents

- [What's actually in this theme](#whats-actually-in-this-theme)
- [The 5 packages, in detail](#the-5-packages-in-detail)
- [Installing on an existing Arch/EndeavourOS system](#installing-on-an-existing-archendeavouros-system)
- [What to expect during install](#what-to-expect-during-install)
- [Building `hrimos-rime-theme` from source, step by step](#building-hrimos-rime-theme-from-source-step-by-step)
- [Configuration: turning the crack overlay on/off](#configuration-turning-the-crack-overlay-onoff)
- [Uninstalling / reverting to stock EndeavourOS](#uninstalling--reverting-to-stock-endeavouros)
- [Building the full HrimOS ISO](#building-the-full-hrimos-iso)
- [Troubleshooting](#troubleshooting)
- [License and attribution](#license-and-attribution)

---

## What's actually in this theme

HrimOS is a fork of the [Darkly](https://github.com/Bali10050/Darkly) KDE application style and window decoration, renamed **Rime**, with a custom-written crack/glow overlay painted directly onto window borders and titlebars — a network of jagged, glowing cyan-blue lines with randomized brightness, meant to evoke fractured ice. This is real C++ code added to the decoration's paint routine (`kdecoration/darklydecoration.cpp`, function `paintCrackOverlay`), not a static image or texture.

Around that base sits:
- A matching dark, icy-blue color scheme (**Rime**)
- A custom folder-icon accent pack (**HrimOS Niflheim**), layered on top of Breeze Dark
- A custom cursor theme (**Hrim Crystalline Frostbite**)
- Original logo, application icon, and wallpaper artwork
- A Plasma "Global Theme" package that ties all of the above together as one selectable option, and sets it as the system default for new user accounts

## The 5 packages, in detail

### `hrimos-rime-theme`
Builds from source (`Rime-src/`, a full fork of Darkly). Provides:
- The **Rime** Qt6 widget style (`darkly6.so`, registered under the style name "Rime")
- The **Rime** KWin window decoration (`org.kde.darkly.so`), including the crack overlay
- The **Rime** color scheme (`Rime.colors`)
- A settings dialog (accessible from Window Decorations' config, or by running `darkly-settings6`) with a checkbox: **"Show icy crack overlay on window borders"** — lets you disable the effect without uninstalling anything.

Depends on: `kdecoration`, `kcolorscheme`, `kiconthemes`, `kirigami`, `frameworkintegration`, `kcmutils`, `kconfig`, `kguiaddons`, `ki18n`, `kwindowsystem`, `qt6-svg`, `qt6-base`. All of these are already part of a standard Plasma desktop install, so on EndeavourOS with KDE you won't need to install anything extra.

### `hrimos-icons`
A small (10-icon) **accent pack**, not a full icon theme — it only replaces folder/places icons (Home, Desktop, Documents, Downloads, Music, Pictures, Videos, Trash, and the generic folder/directory icons) with frost-rune artwork, and inherits Breeze Dark for every other icon (applications, mimetypes, status, etc.). This is why it depends on `breeze-icons`.

### `hrimos-cursors`
A cursor theme where most cursors are original custom artwork. Where a custom cursor glyph was left unfinished, the theme falls back to a modified version of **BreezeX Dark** (see [License and attribution](#license-and-attribution) — this part is GPL-3.0). Depends on `hicolor-icon-theme`.

### `hrimos-branding`
Just static assets, no logic:
- `/usr/share/pixmaps/hrimos-logo.png` — the full HrimOS logo/wordmark
- `/usr/share/icons/hicolor/512x512/apps/hrimos-home.png` — the "H" glyph, used as the Kickoff/application-launcher icon
- `/usr/share/wallpapers/HrimOS/contents/images/1920x1080.png` — the default wallpaper, packaged as a proper Plasma wallpaper (so it shows up correctly in the wallpaper picker, not just as a loose file)

### `hrimos-meta`
The package that actually makes HrimOS the *default* rather than just an option sitting in a list. It:
- Depends on all four packages above, so installing `hrimos-meta` alone pulls in everything
- Ships a proper Plasma **Global Theme** (Look-and-Feel) package at `/usr/share/plasma/look-and-feel/com.hrimos.rime.desktop`, which shows up in System Settings → Global Theme as **"HrimOS Rime"**
- That Global Theme package includes a real Plasma panel layout script (`org.kde.plasma.desktop-layout.js`) that sets the Kickoff launcher's icon to the H glyph automatically
- Sets `/etc/xdg/kdeglobals`, `/etc/xdg/kwinrc`, and `/etc/xdg/kcminputrc` as **system-wide fallback defaults** — this is what makes a brand-new user account boot straight into the full HrimOS look with zero manual configuration
- **Replaces `eos-settings-plasma`** (EndeavourOS's own default-branding package). This is intentional and expected — see the note on the install prompt below.

Important nuance: Plasma always prioritizes a user's *own* saved settings over these system-wide `/etc/xdg` fallbacks. So installing `hrimos-meta` on an **existing, already-configured** user account will not visually change anything for that account — it only affects accounts that don't already have their own settings (i.e., new users, or a fresh install). Existing users can still switch to the HrimOS look manually via System Settings → Global Theme → HrimOS Rime.

## Installing on an existing Arch/EndeavourOS system

Clone this repo, then build and install each package with `makepkg`:

```bash
git clone https://github.com/ilostecho404/HrimOS.git
cd HrimOS
```

**Install in this order** — later packages depend on earlier ones, and `makepkg -si` will pull in build dependencies automatically:

```bash
cd hrimos-cursors      && makepkg -si && cd ..
cd hrimos-icons        && makepkg -si && cd ..
cd hrimos-branding     && makepkg -si && cd ..
cd hrimos-rime-theme   && makepkg -si && cd ..
cd hrimos-meta         && makepkg -si && cd ..
```

Each `makepkg -si` will:
1. Build the package (for `hrimos-rime-theme`, this compiles C++ and can take a few minutes)
2. Ask to confirm installing any missing build/runtime dependencies
3. Ask to confirm installing the final package
4. Install it with `sudo pacman -U`

## What to expect during install

**When you get to `hrimos-meta`**, pacman will print something like:

```
:: hrimos-meta-1.0.0-1 and eos-settings-plasma-2.1-1 are in conflict. Remove eos-settings-plasma? [y/N]
```

**This is expected — type `y`.** `hrimos-meta` is meant to replace EndeavourOS's default branding package with HrimOS's own. This does not remove any actual desktop functionality, only EndeavourOS's default panel-layout/branding config, which `hrimos-meta` supplies its own replacement for.

If you're building `hrimos-rime-theme` and see two `fatal: not a git repository` lines during the CMake configure step — **that's harmless**. It's the upstream build system trying (and failing) to read a git commit hash for its version string, since this is a source tarball copy rather than a live git checkout. The build continues normally afterward.

## Building `hrimos-rime-theme` from source, step by step

If you want to build this package manually instead of via `makepkg -si` (for example, to test changes to the decoration code), here's what's happening under the hood:

```bash
cd hrimos-rime-theme/Rime-src
mkdir build && cd build

cmake .. \
    -DCMAKE_INSTALL_PREFIX=/usr \
    -DCMAKE_BUILD_TYPE=Release \
    -DBUILD_QT6=ON \
    -DBUILD_QT5=OFF

make -j$(nproc)
sudo make install
```

- `-DBUILD_QT6=ON -DBUILD_QT5=OFF` is important: the upstream Darkly `CMakeLists.txt` defaults to attempting a Qt5 build too, which will fail on a modern Plasma 6 system that doesn't have Qt5 dev packages installed. This flag combination builds only the Qt6/KF6 variant.
- Build dependencies you'll need if building manually (not via `makepkg`, which handles this automatically): `extra-cmake-modules`, `cmake`, `qt6-base`, `qt6-declarative`, `qt6-svg`, and the KDE Frameworks packages listed under `hrimos-rime-theme`'s `depends` above (as `-dev`/full packages, not just runtime).

The crack overlay itself lives entirely in one function: `Decoration::paintCrackOverlay()` in `kdecoration/darklydecoration.cpp`. It's a from-scratch procedural generator — a seeded random-number generator (so the pattern is stable across repaints on a given window, rather than regenerating every time the window redraws) scatters short jittered line segments along each border edge, with per-scratch brightness/color variation for the glow effect. If you want to tweak the look, that function is the place to do it.

## Configuration: turning the crack overlay on/off

Right-click any window's titlebar → **Configure Window Decoration** (or open System Settings → Appearance → Window Decorations → the pencil/edit icon next to "Rime"), and look for the checkbox **"Show icy crack overlay on window borders."** This is stored as a normal Plasma setting (`ShowIceCracks` in `darklysettingsdata.kcfg`), so it persists across reboots and doesn't require touching any files by hand.

## Uninstalling / reverting to stock EndeavourOS

```bash
sudo pacman -R hrimos-meta hrimos-rime-theme hrimos-branding hrimos-icons hrimos-cursors
sudo pacman -S eos-settings-plasma
```

Then switch your Colors, Application Style, Window Decorations, Icons, and Cursors back to EndeavourOS's defaults (Breeze Dark / Breeze) via System Settings, or select the "Breeze Dark EndeavourOS" Global Theme, which restores everything at once.

## Building the full HrimOS ISO

HrimOS also ships as a standalone bootable installer, built on top of EndeavourOS's own official ISO framework ([endeavouros-team/EndeavourOS-ISO](https://github.com/endeavouros-team/EndeavourOS-ISO)), rather than a from-scratch archiso profile — this keeps HrimOS's installer environment, mirror handling, and Calamares integration in sync with upstream EndeavourOS releases.

This needs to be built on a real Arch-based machine (or a full VM) with actual root access — it will **not** work correctly inside most containers, since ISO building needs loop devices and squashfs tools that containers typically can't use properly.

```bash
sudo pacman -S --needed archiso git squashfs-tools
git clone https://github.com/endeavouros-team/EndeavourOS-ISO.git
cd EndeavourOS-ISO
./prepare.sh
```

`prepare.sh` does its own housekeeping (dates the live wallpaper, fetches EndeavourOS's own default branding package into `airootfs/root/packages/`, builds a live-session skeleton package). **Before building, remove that default branding package and drop in HrimOS's own packages instead:**

```bash
rm airootfs/root/packages/eos-settings-plasma-*.pkg.tar.zst

cp /path/to/HrimOS/hrimos-rime-theme/hrimos-rime-theme-*.pkg.tar.zst \
   /path/to/HrimOS/hrimos-icons/hrimos-icons-*.pkg.tar.zst \
   /path/to/HrimOS/hrimos-branding/hrimos-branding-*.pkg.tar.zst \
   /path/to/HrimOS/hrimos-meta/hrimos-meta-*.pkg.tar.zst \
   /path/to/HrimOS/hrimos-cursors/hrimos-cursors-*.pkg.tar.zst \
   airootfs/root/packages/
```

(Build each of those five packages first with `makepkg` if you haven't already — see [Installing](#installing-on-an-existing-archendeavouros-system) above.)

Everything placed in `airootfs/root/packages/` gets installed automatically during the ISO build (via a single `pacman -U` covering the whole directory), and the directory is cleaned out afterward — this is EndeavourOS's own documented mechanism for including locally-built packages, we're not working around anything here.

Then build:

```bash
sudo ./mkarchiso -v "." 2>&1 | tee "hrimos-iso-build-$(date -u +'%Y.%m.%d-%H:%M').log"
```

This takes a while (expect somewhere from 20 minutes to over an hour depending on connection speed and hardware) and needs several GB of free disk space for the build's working directory. The finished `.iso` file appears in the `out/` directory when it's done.

## Troubleshooting

- **`pacman -Syu` fails partway through with "Operation too slow" on a specific mirror** — this is a transient mirror issue unrelated to HrimOS. Re-run the command; pacman will typically pick a different mirror on retry, or wait a few minutes and try again.
- **Everything looks like stock Breeze after installing `hrimos-meta`** — check whether you're testing on an account that already has its own saved Plasma settings (see the note under `hrimos-meta` above). Test on a genuinely new user account, or manually select "HrimOS Rime" from Global Theme.
- **The crack overlay looks different / bolder / more subtle on your specific window sizes** — this is intentional; the effect is randomized per-window and scales with window/border dimensions, so it won't look pixel-identical on every window.

## License and attribution

- `hrimos-rime-theme` is a fork of [Darkly](https://github.com/Bali10050/Darkly) (itself descended from [Lightly](https://github.com/Bali10050/Lightly)), licensed **GPL-2.0**.
- `hrimos-cursors` incorporates a modified version of [BreezeX Dark](https://github.com/ful1e5/BreezeX_Cursor) by ful1e5, licensed **GPL-3.0**, used as a fallback for incomplete custom cursor glyphs. Full attribution and license text ship inside the package at `/usr/share/icons/Hrim-Crystalline-Frostbite/ATTRIBUTION.md` and `/usr/share/licenses/hrimos-cursors/LICENSE`.
- `hrimos-icons` inherits Breeze Dark (KDE, LGPL) for all icons outside its own custom folder set.
- The ISO build framework is [endeavouros-team/EndeavourOS-ISO](https://github.com/endeavouros-team/EndeavourOS-ISO), used and modified per its own license.
- All other original artwork (logo, H icon, wallpaper, custom cursor glyphs, crack overlay code) is original work created for HrimOS.
