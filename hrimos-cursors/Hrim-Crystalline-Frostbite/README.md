# Hrim Crystalline Frostbite

A merged KDE Plasma cursor theme:

- **Base look**: all 56 cursor names from *Hrim OS Crystalline Breeze* are kept
  exactly as they were (same bitmaps, same hotspots, same internal aliasing).
- **Coverage fill-in**: *BreezeX-Frostbite-Dark* is used to add the ~90 cursor
  names Hrim didn't define (legacy X11 hash-named cursors, scrollbar arrows,
  window-edge/corner resize handles, dnd/no-drop variants, help/question
  cursors, etc.), so apps and toolkits that look up less-common cursor names
  won't fall back to a mismatched system default.
  - Wherever a missing name's *meaning* matched something Hrim already draws
    (e.g. "question_arrow" ≈ Hrim's "help", "sb_h_double_arrow" ≈ Hrim's
    "ew-resize", the corner-resize cursors ≈ Hrim's diagonal resize cursors),
    it was aliased to Hrim's own artwork instead of importing a different-
    looking cursor, to keep the theme visually consistent.
  - Only genuinely new shapes with no Hrim equivalent were imported as real
    bitmaps from BreezeX-Frostbite-Dark: `X_cursor`, `color-picker`, `dotbox`,
    `pencil`, `pirate`, `plus`, and the four scrollbar arrows
    (`sb_up_arrow`, `sb_down_arrow`, `sb_left_arrow`, `sb_right_arrow`).

Result: 146 cursor names total, zero broken symlinks, full parity with
BreezeX-Frostbite-Dark's name coverage.

## Install in KDE Plasma
System Settings → Colors & Themes → Cursors → Install from File…
Select `Hrim-Crystalline-Frostbite.tar.gz`, then choose **Hrim Crystalline Frostbite**.

## Credits
- Cursor artwork: Hrim OS Crystalline Breeze (primary) and BreezeX-Frostbite-Dark
  (coverage fill-in), per their original licenses.
