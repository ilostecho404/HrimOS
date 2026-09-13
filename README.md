# HrimOS

*Between worlds, we rest.*

HrimOS is a themed KDE Plasma look-and-feel built on top of [EndeavourOS](https://endeavouros.com/), featuring an icy, cracked-ice/lightning aesthetic across the window style, decoration, colors, icons, cursors, and branding.

This repository contains the Arch Linux packages (`PKGBUILD`s) used to build and install the full HrimOS look on any Arch-based system.

## Packages

| Package | What it provides |
|---|---|
| `hrimos-rime-theme` | The **Rime** application style, window decoration, and color scheme — a fork of [Bali10050/Darkly](https://github.com/Bali10050/Darkly) with a custom icy crack/glow overlay painted on window borders and titlebars. |
| `hrimos-icons` | **HrimOS Niflheim** — a frost-rune folder icon accent pack (inherits Breeze Dark for everything else). |
| `hrimos-cursors` | **Hrim Crystalline Frostbite** — a custom cursor theme, with a modified BreezeX Dark used as a fallback for any incomplete custom glyphs. |
| `hrimos-branding` | The HrimOS logo, the H-glyph application icon, and the default wallpaper. |
| `hrimos-meta` | Ties everything together: depends on the four packages above, ships a proper Plasma "Global Theme" (Look-and-Feel) package (`com.hrimos.rime.desktop`), and sets these as the system-wide defaults for any new user account. Replaces `eos-settings-plasma`. |

## Installing

Each package can be built and installed individually with `makepkg`:

```bash
cd hrimos-cursors
makepkg -si
```

**Recommended order** (later packages depend on earlier ones):

```bash
cd hrimos-cursors   && makepkg -si && cd ..
cd hrimos-icons      && makepkg -si && cd ..
cd hrimos-branding   && makepkg -si && cd ..
cd hrimos-rime-theme && makepkg -si && cd ..
cd hrimos-meta       && makepkg -si && cd ..
```

`hrimos-meta` will prompt to replace `eos-settings-plasma` — this is expected and intentional; it's how HrimOS's defaults take over from EndeavourOS's stock branding.

After installing `hrimos-meta`, log out and log back in (or create a fresh user account) to see the full default theme applied automatically. Existing user accounts with their own saved settings will **not** be overridden — Plasma always prioritizes a user's own config over system defaults, so already-configured accounts can pick up the new look manually via System Settings → Colors & Themes → Global Theme → **HrimOS Rime**.

## License and attribution

- `hrimos-rime-theme` is a fork of [Darkly](https://github.com/Bali10050/Darkly) (itself descended from [Lightly](https://github.com/Bali10050/Lightly)), licensed **GPL-2.0**.
- `hrimos-cursors` incorporates a modified version of [BreezeX Dark](https://github.com/ful1e5/BreezeX_Cursor) by ful1e5, licensed **GPL-3.0**, used as a fallback for incomplete custom cursor glyphs. Full attribution and license text ship inside the package at `/usr/share/icons/Hrim-Crystalline-Frostbite/ATTRIBUTION.md` and `/usr/share/licenses/hrimos-cursors/LICENSE`.
- `hrimos-icons` inherits Breeze Dark (KDE, LGPL) for all icons outside its own custom folder set.
- All other original artwork (logo, H icon, wallpaper, custom cursor glyphs, crack overlay code) is original work created for HrimOS.

## Building the distro ISO

These packages are the theming layer for a full EndeavourOS-based HrimOS respin. ISO build tooling and instructions will be added to this repository separately.
