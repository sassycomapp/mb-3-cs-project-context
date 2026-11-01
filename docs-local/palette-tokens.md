---
title: "palette-tokens"
doc-id: "mb-8f719d7c"
updated: "2026-11-01"
---
# Mybizz CS — Palette Tokens

This document is the single home of the colour values of the four Mybizz CS palettes, in light and dark. `DESIGN.md` defines how colour is used; this document holds the values.

---

## 1. How the Palettes Are Made

- Each palette is generated from its locked source colour with the Material 3 HCT algorithm, scheme **Tonal Spot**, Material colour specification 2021, contrast level standard — the same colour system the Anvil Material 3 theme is built on.
- Each palette is a complete Anvil Material 3 colour scheme: the 46 colours of the Anvil M3 theme's Colour Scheme, with the same names, in light and in dark.
- Overlay and disabled colours follow the Anvil M3 rules: Primary Overlay 1/2/3 are Primary at 5/8/12%; On Disabled, Disabled Container and Disabled Container 2 are On Surface at 38/12/4%; Light Overlay 1/2 are white at 8/12% in light and On Secondary Container at 8/12% in dark; Dark Overlay 1/2/3 are On Secondary Container at 8/12/16%; Error Overlay 1/2 are Error at 8/12%; the On Surface Variant and Inverse On Surface overlays are those colours at 8/12%.
- Every palette meets WCAG AA contrast for text on its main colours; the measured contrast is stated above each block.
- The values are design constants. They are changed only by regenerating all four palettes from the source colours, never by editing a single value.

### Locked source colours

| Palette | Key | Source colour |
|---|---|---|
| Professional Blue (default) | `professional_blue` | `#2563EB` |
| Warm Coral | `warm_coral` | `#FF6F61` |
| Calm Teal | `calm_teal` | `#008080` |
| Sophisticated Purple | `sophisticated_purple` | `#6B46C1` |

---

## 2. How the Palettes Are Applied

**Default palette.** The Professional Blue light values are entered into the app's Colour Scheme (Theme → Colour Scheme) in Apps 1, 2, 3 and 5. That makes Professional Blue what every screen shows before a palette is applied, and what the Anvil Designer shows.

**Runtime palette.** The Anvil M3 theme exposes every Colour Scheme colour as a CSS custom property on `:root`, named `--anvil-m3-` followed by the colour name in lower case with spaces replaced by hyphens (Primary Container → `--anvil-m3-primary-container`). Every native M3 component reads these properties, so setting them recolours the whole app. The project's own roles in `theme.css` use the same `--anvil-m3-*` properties, never separate colour variables.

**Dark mode.** Dark mode follows the device setting on every screen ([[[default:adr-global/adr-dark-mode|Dark Mode]]](C:/dev/project-library-global/adr-global/adr-dark-mode.md) ADR). The palette module applies the palette's dark block when the device prefers dark, and switches automatically when the device setting changes.

**Status colours** (`status-success`, `status-pending`, `status-error`) are fixed platform colours, not palette colours ([[[default:specifications-global/spec-m3-design-standards|M3 Design & Component Standards]]](C:/dev/project-library-global/specifications-global/spec-m3-design-standards.md) section 10). They are added once as custom colours in the Colour Scheme and are the same in every palette.

### Palette module (`client_code/shared/palette_utils.py`)

```python
from anvil.js import window

def _css_name(colour_name):
    return "--anvil-m3-" + colour_name.lower().replace(" ", "-")

def apply_palette(palette_key):
    """Apply a palette, choosing light or dark from the device setting."""
    dark = window.matchMedia("(prefers-color-scheme: dark)").matches
    tokens = PALETTES[palette_key + ("_dark" if dark else "")]
    style = window.document.documentElement.style
    for colour_name, value in tokens.items():
        style.setProperty(_css_name(colour_name), value)

def watch_device_theme(palette_key):
    """Re-apply the palette when the device switches between light and dark."""
    query = window.matchMedia("(prefers-color-scheme: dark)")
    query.addEventListener("change", lambda event: apply_palette(palette_key))
```

- The palette key is stored in `business_profile.palette`; the startup module calls `apply_palette` and `watch_device_theme` with it on every page load, public and admin.
- No `eval` is used.
- `PALETTES` holds the eight blocks below.

---

## 3. Palette Blocks

### Professional Blue

- Source colour: `#2563EB`
- Best for: consultants, clinics, therapists.
- Intended effect: trust, professionalism, stability.

**Light** — contrast: On Primary on Primary 6.5:1; On Surface on Surface 16.3:1; On Primary Container on Primary Container 7.3:1.

```python
'professional_blue': {
    'Primary':                                    '#4B5C92',
    'Primary Container':                          '#DBE1FF',
    'On Primary':                                 '#FFFFFF',
    'On Primary Container':                       '#324478',
    'Secondary':                                  '#595E72',
    'Secondary Container':                        '#DDE1F9',
    'On Secondary':                               '#FFFFFF',
    'On Secondary Container':                     '#414659',
    'Tertiary':                                   '#745470',
    'Tertiary Container':                         '#FFD6F8',
    'On Tertiary':                                '#FFFFFF',
    'On Tertiary Container':                      '#5A3D58',
    'Error':                                      '#BA1A1A',
    'On Error':                                   '#FFFFFF',
    'On Error Container':                         '#93000A',
    'Background':                                 '#FAF8FF',
    'On Background':                              '#1A1B21',
    'Surface':                                    '#FAF8FF',
    'On Surface':                                 '#1A1B21',
    'Surface Variant':                            '#E2E2EC',
    'On Surface Variant':                         '#45464F',
    'Outline':                                    '#757680',
    'Outline Variant':                            '#C5C6D0',
    'Surface Container Low':                      '#F4F3FA',
    'Surface Container':                          '#EEEDF4',
    'Surface Container High':                     '#E8E7EF',
    'Surface Container Highest':                  '#E3E2E9',
    'Inverse Surface':                            '#2F3036',
    'Inverse On Surface':                         '#F1F0F7',
    'Primary Overlay 1':                          'rgba(75, 92, 146, 0.05)',
    'Primary Overlay 2':                          'rgba(75, 92, 146, 0.08)',
    'Primary Overlay 3':                          'rgba(75, 92, 146, 0.12)',
    'On Disabled':                                'rgba(26, 27, 33, 0.38)',
    'Disabled Container':                         'rgba(26, 27, 33, 0.12)',
    'Disabled Container 2':                       'rgba(26, 27, 33, 0.04)',
    'Light Overlay 1':                            'rgba(255, 255, 255, 0.08)',
    'Light Overlay 2':                            'rgba(255, 255, 255, 0.12)',
    'Dark Overlay 1':                             'rgba(65, 70, 89, 0.08)',
    'Dark Overlay 2':                             'rgba(65, 70, 89, 0.12)',
    'Dark Overlay 3':                             'rgba(65, 70, 89, 0.16)',
    'Error Overlay 1':                            'rgba(186, 26, 26, 0.08)',
    'Error Overlay 2':                            'rgba(186, 26, 26, 0.12)',
    'On Surface Variant Overlay 1':               'rgba(69, 70, 79, 0.08)',
    'On Surface Variant Overlay 2':               'rgba(69, 70, 79, 0.12)',
    'Inverse On Surface Overlay 1':               'rgba(241, 240, 247, 0.08)',
    'Inverse On Surface Overlay 2':               'rgba(241, 240, 247, 0.12)',
},
```

**Dark** — contrast: On Primary on Primary 7.8:1; On Surface on Surface 14.4:1; On Primary Container on Primary Container 7.3:1.

```python
'professional_blue_dark': {
    'Primary':                                    '#B4C5FF',
    'Primary Container':                          '#324478',
    'On Primary':                                 '#1A2D60',
    'On Primary Container':                       '#DBE1FF',
    'Secondary':                                  '#C1C5DD',
    'Secondary Container':                        '#414659',
    'On Secondary':                               '#2B3042',
    'On Secondary Container':                     '#DDE1F9',
    'Tertiary':                                   '#E2BBDB',
    'Tertiary Container':                         '#5A3D58',
    'On Tertiary':                                '#422741',
    'On Tertiary Container':                      '#FFD6F8',
    'Error':                                      '#FFB4AB',
    'On Error':                                   '#690005',
    'On Error Container':                         '#FFDAD6',
    'Background':                                 '#121318',
    'On Background':                              '#E3E2E9',
    'Surface':                                    '#121318',
    'On Surface':                                 '#E3E2E9',
    'Surface Variant':                            '#45464F',
    'On Surface Variant':                         '#C5C6D0',
    'Outline':                                    '#8F909A',
    'Outline Variant':                            '#45464F',
    'Surface Container Low':                      '#1A1B21',
    'Surface Container':                          '#1E1F25',
    'Surface Container High':                     '#292A2F',
    'Surface Container Highest':                  '#34343A',
    'Inverse Surface':                            '#E3E2E9',
    'Inverse On Surface':                         '#2F3036',
    'Primary Overlay 1':                          'rgba(180, 197, 255, 0.05)',
    'Primary Overlay 2':                          'rgba(180, 197, 255, 0.08)',
    'Primary Overlay 3':                          'rgba(180, 197, 255, 0.12)',
    'On Disabled':                                'rgba(227, 226, 233, 0.38)',
    'Disabled Container':                         'rgba(227, 226, 233, 0.12)',
    'Disabled Container 2':                       'rgba(227, 226, 233, 0.04)',
    'Light Overlay 1':                            'rgba(221, 225, 249, 0.08)',
    'Light Overlay 2':                            'rgba(221, 225, 249, 0.12)',
    'Dark Overlay 1':                             'rgba(221, 225, 249, 0.08)',
    'Dark Overlay 2':                             'rgba(221, 225, 249, 0.12)',
    'Dark Overlay 3':                             'rgba(221, 225, 249, 0.16)',
    'Error Overlay 1':                            'rgba(255, 180, 171, 0.08)',
    'Error Overlay 2':                            'rgba(255, 180, 171, 0.12)',
    'On Surface Variant Overlay 1':               'rgba(197, 198, 208, 0.08)',
    'On Surface Variant Overlay 2':               'rgba(197, 198, 208, 0.12)',
    'Inverse On Surface Overlay 1':               'rgba(47, 48, 54, 0.08)',
    'Inverse On Surface Overlay 2':               'rgba(47, 48, 54, 0.12)',
},
```

### Warm Coral

- Source colour: `#FF6F61`
- Best for: salons, spas, lifestyle, beauty.
- Intended effect: warmth, creativity, approachability.

**Light** — contrast: On Primary on Primary 6.5:1; On Surface on Surface 16.4:1; On Primary Container on Primary Container 7.2:1.

```python
'warm_coral': {
    'Primary':                                    '#904A42',
    'Primary Container':                          '#FFDAD5',
    'On Primary':                                 '#FFFFFF',
    'On Primary Container':                       '#73342D',
    'Secondary':                                  '#775652',
    'Secondary Container':                        '#FFDAD5',
    'On Secondary':                               '#FFFFFF',
    'On Secondary Container':                     '#5D3F3B',
    'Tertiary':                                   '#715B2E',
    'Tertiary Container':                         '#FDDFA6',
    'On Tertiary':                                '#FFFFFF',
    'On Tertiary Container':                      '#574419',
    'Error':                                      '#BA1A1A',
    'On Error':                                   '#FFFFFF',
    'On Error Container':                         '#93000A',
    'Background':                                 '#FFF8F7',
    'On Background':                              '#231918',
    'Surface':                                    '#FFF8F7',
    'On Surface':                                 '#231918',
    'Surface Variant':                            '#F5DDDA',
    'On Surface Variant':                         '#534341',
    'Outline':                                    '#857370',
    'Outline Variant':                            '#D8C2BF',
    'Surface Container Low':                      '#FFF0EE',
    'Surface Container':                          '#FCEAE7',
    'Surface Container High':                     '#F6E4E2',
    'Surface Container Highest':                  '#F1DEDC',
    'Inverse Surface':                            '#392E2D',
    'Inverse On Surface':                         '#FFEDEA',
    'Primary Overlay 1':                          'rgba(144, 74, 66, 0.05)',
    'Primary Overlay 2':                          'rgba(144, 74, 66, 0.08)',
    'Primary Overlay 3':                          'rgba(144, 74, 66, 0.12)',
    'On Disabled':                                'rgba(35, 25, 24, 0.38)',
    'Disabled Container':                         'rgba(35, 25, 24, 0.12)',
    'Disabled Container 2':                       'rgba(35, 25, 24, 0.04)',
    'Light Overlay 1':                            'rgba(255, 255, 255, 0.08)',
    'Light Overlay 2':                            'rgba(255, 255, 255, 0.12)',
    'Dark Overlay 1':                             'rgba(93, 63, 59, 0.08)',
    'Dark Overlay 2':                             'rgba(93, 63, 59, 0.12)',
    'Dark Overlay 3':                             'rgba(93, 63, 59, 0.16)',
    'Error Overlay 1':                            'rgba(186, 26, 26, 0.08)',
    'Error Overlay 2':                            'rgba(186, 26, 26, 0.12)',
    'On Surface Variant Overlay 1':               'rgba(83, 67, 65, 0.08)',
    'On Surface Variant Overlay 2':               'rgba(83, 67, 65, 0.12)',
    'Inverse On Surface Overlay 1':               'rgba(255, 237, 234, 0.08)',
    'Inverse On Surface Overlay 2':               'rgba(255, 237, 234, 0.12)',
},
```

**Dark** — contrast: On Primary on Primary 7.7:1; On Surface on Surface 14.3:1; On Primary Container on Primary Container 7.2:1.

```python
'warm_coral_dark': {
    'Primary':                                    '#FFB4AA',
    'Primary Container':                          '#73342D',
    'On Primary':                                 '#561E19',
    'On Primary Container':                       '#FFDAD5',
    'Secondary':                                  '#E7BDB7',
    'Secondary Container':                        '#5D3F3B',
    'On Secondary':                               '#442926',
    'On Secondary Container':                     '#FFDAD5',
    'Tertiary':                                   '#DFC38C',
    'Tertiary Container':                         '#574419',
    'On Tertiary':                                '#3F2E04',
    'On Tertiary Container':                      '#FDDFA6',
    'Error':                                      '#FFB4AB',
    'On Error':                                   '#690005',
    'On Error Container':                         '#FFDAD6',
    'Background':                                 '#1A1110',
    'On Background':                              '#F1DEDC',
    'Surface':                                    '#1A1110',
    'On Surface':                                 '#F1DEDC',
    'Surface Variant':                            '#534341',
    'On Surface Variant':                         '#D8C2BF',
    'Outline':                                    '#A08C8A',
    'Outline Variant':                            '#534341',
    'Surface Container Low':                      '#231918',
    'Surface Container':                          '#271D1C',
    'Surface Container High':                     '#322826',
    'Surface Container Highest':                  '#3D3231',
    'Inverse Surface':                            '#F1DEDC',
    'Inverse On Surface':                         '#392E2D',
    'Primary Overlay 1':                          'rgba(255, 180, 170, 0.05)',
    'Primary Overlay 2':                          'rgba(255, 180, 170, 0.08)',
    'Primary Overlay 3':                          'rgba(255, 180, 170, 0.12)',
    'On Disabled':                                'rgba(241, 222, 220, 0.38)',
    'Disabled Container':                         'rgba(241, 222, 220, 0.12)',
    'Disabled Container 2':                       'rgba(241, 222, 220, 0.04)',
    'Light Overlay 1':                            'rgba(255, 218, 213, 0.08)',
    'Light Overlay 2':                            'rgba(255, 218, 213, 0.12)',
    'Dark Overlay 1':                             'rgba(255, 218, 213, 0.08)',
    'Dark Overlay 2':                             'rgba(255, 218, 213, 0.12)',
    'Dark Overlay 3':                             'rgba(255, 218, 213, 0.16)',
    'Error Overlay 1':                            'rgba(255, 180, 171, 0.08)',
    'Error Overlay 2':                            'rgba(255, 180, 171, 0.12)',
    'On Surface Variant Overlay 1':               'rgba(216, 194, 191, 0.08)',
    'On Surface Variant Overlay 2':               'rgba(216, 194, 191, 0.12)',
    'Inverse On Surface Overlay 1':               'rgba(57, 46, 45, 0.08)',
    'Inverse On Surface Overlay 2':               'rgba(57, 46, 45, 0.12)',
},
```

### Calm Teal

- Source colour: `#008080`
- Best for: fitness, wellness, physiotherapy, health services.
- Intended effect: health, balance, growth.

**Light** — contrast: On Primary on Primary 6.4:1; On Surface on Surface 16.3:1; On Primary Container on Primary Container 7.3:1.

```python
'calm_teal': {
    'Primary':                                    '#006A6A',
    'Primary Container':                          '#9CF1F0',
    'On Primary':                                 '#FFFFFF',
    'On Primary Container':                       '#004F4F',
    'Secondary':                                  '#4A6363',
    'Secondary Container':                        '#CCE8E7',
    'On Secondary':                               '#FFFFFF',
    'On Secondary Container':                     '#324B4B',
    'Tertiary':                                   '#4B607C',
    'Tertiary Container':                         '#D3E4FF',
    'On Tertiary':                                '#FFFFFF',
    'On Tertiary Container':                      '#334863',
    'Error':                                      '#BA1A1A',
    'On Error':                                   '#FFFFFF',
    'On Error Container':                         '#93000A',
    'Background':                                 '#F4FBFA',
    'On Background':                              '#161D1D',
    'Surface':                                    '#F4FBFA',
    'On Surface':                                 '#161D1D',
    'Surface Variant':                            '#DAE5E4',
    'On Surface Variant':                         '#3F4948',
    'Outline':                                    '#6F7979',
    'Outline Variant':                            '#BEC9C8',
    'Surface Container Low':                      '#EFF5F4',
    'Surface Container':                          '#E9EFEE',
    'Surface Container High':                     '#E3E9E9',
    'Surface Container Highest':                  '#DDE4E3',
    'Inverse Surface':                            '#2B3231',
    'Inverse On Surface':                         '#ECF2F1',
    'Primary Overlay 1':                          'rgba(0, 106, 106, 0.05)',
    'Primary Overlay 2':                          'rgba(0, 106, 106, 0.08)',
    'Primary Overlay 3':                          'rgba(0, 106, 106, 0.12)',
    'On Disabled':                                'rgba(22, 29, 29, 0.38)',
    'Disabled Container':                         'rgba(22, 29, 29, 0.12)',
    'Disabled Container 2':                       'rgba(22, 29, 29, 0.04)',
    'Light Overlay 1':                            'rgba(255, 255, 255, 0.08)',
    'Light Overlay 2':                            'rgba(255, 255, 255, 0.12)',
    'Dark Overlay 1':                             'rgba(50, 75, 75, 0.08)',
    'Dark Overlay 2':                             'rgba(50, 75, 75, 0.12)',
    'Dark Overlay 3':                             'rgba(50, 75, 75, 0.16)',
    'Error Overlay 1':                            'rgba(186, 26, 26, 0.08)',
    'Error Overlay 2':                            'rgba(186, 26, 26, 0.12)',
    'On Surface Variant Overlay 1':               'rgba(63, 73, 72, 0.08)',
    'On Surface Variant Overlay 2':               'rgba(63, 73, 72, 0.12)',
    'Inverse On Surface Overlay 1':               'rgba(236, 242, 241, 0.08)',
    'Inverse On Surface Overlay 2':               'rgba(236, 242, 241, 0.12)',
},
```

**Dark** — contrast: On Primary on Primary 7.7:1; On Surface on Surface 14.3:1; On Primary Container on Primary Container 7.3:1.

```python
'calm_teal_dark': {
    'Primary':                                    '#80D5D4',
    'Primary Container':                          '#004F4F',
    'On Primary':                                 '#003737',
    'On Primary Container':                       '#9CF1F0',
    'Secondary':                                  '#B0CCCB',
    'Secondary Container':                        '#324B4B',
    'On Secondary':                               '#1B3534',
    'On Secondary Container':                     '#CCE8E7',
    'Tertiary':                                   '#B3C8E8',
    'Tertiary Container':                         '#334863',
    'On Tertiary':                                '#1C314B',
    'On Tertiary Container':                      '#D3E4FF',
    'Error':                                      '#FFB4AB',
    'On Error':                                   '#690005',
    'On Error Container':                         '#FFDAD6',
    'Background':                                 '#0E1514',
    'On Background':                              '#DDE4E3',
    'Surface':                                    '#0E1514',
    'On Surface':                                 '#DDE4E3',
    'Surface Variant':                            '#3F4948',
    'On Surface Variant':                         '#BEC9C8',
    'Outline':                                    '#889392',
    'Outline Variant':                            '#3F4948',
    'Surface Container Low':                      '#161D1D',
    'Surface Container':                          '#1A2121',
    'Surface Container High':                     '#252B2B',
    'Surface Container Highest':                  '#2F3636',
    'Inverse Surface':                            '#DDE4E3',
    'Inverse On Surface':                         '#2B3231',
    'Primary Overlay 1':                          'rgba(128, 213, 212, 0.05)',
    'Primary Overlay 2':                          'rgba(128, 213, 212, 0.08)',
    'Primary Overlay 3':                          'rgba(128, 213, 212, 0.12)',
    'On Disabled':                                'rgba(221, 228, 227, 0.38)',
    'Disabled Container':                         'rgba(221, 228, 227, 0.12)',
    'Disabled Container 2':                       'rgba(221, 228, 227, 0.04)',
    'Light Overlay 1':                            'rgba(204, 232, 231, 0.08)',
    'Light Overlay 2':                            'rgba(204, 232, 231, 0.12)',
    'Dark Overlay 1':                             'rgba(204, 232, 231, 0.08)',
    'Dark Overlay 2':                             'rgba(204, 232, 231, 0.12)',
    'Dark Overlay 3':                             'rgba(204, 232, 231, 0.16)',
    'Error Overlay 1':                            'rgba(255, 180, 171, 0.08)',
    'Error Overlay 2':                            'rgba(255, 180, 171, 0.12)',
    'On Surface Variant Overlay 1':               'rgba(190, 201, 200, 0.08)',
    'On Surface Variant Overlay 2':               'rgba(190, 201, 200, 0.12)',
    'Inverse On Surface Overlay 1':               'rgba(43, 50, 49, 0.08)',
    'Inverse On Surface Overlay 2':               'rgba(43, 50, 49, 0.12)',
},
```

### Sophisticated Purple

- Source colour: `#6B46C1`
- Best for: executive coaching, premium consulting, high-end services.
- Intended effect: premium, refined, exclusive.

**Light** — contrast: On Primary on Primary 6.5:1; On Surface on Surface 16.2:1; On Primary Container on Primary Container 7.3:1.

```python
'sophisticated_purple': {
    'Primary':                                    '#65558F',
    'Primary Container':                          '#E9DDFF',
    'On Primary':                                 '#FFFFFF',
    'On Primary Container':                       '#4D3D75',
    'Secondary':                                  '#625B70',
    'Secondary Container':                        '#E8DEF8',
    'On Secondary':                               '#FFFFFF',
    'On Secondary Container':                     '#4A4458',
    'Tertiary':                                   '#7E525F',
    'Tertiary Container':                         '#FFD9E2',
    'On Tertiary':                                '#FFFFFF',
    'On Tertiary Container':                      '#633B48',
    'Error':                                      '#BA1A1A',
    'On Error':                                   '#FFFFFF',
    'On Error Container':                         '#93000A',
    'Background':                                 '#FEF7FF',
    'On Background':                              '#1D1B20',
    'Surface':                                    '#FEF7FF',
    'On Surface':                                 '#1D1B20',
    'Surface Variant':                            '#E7E0EB',
    'On Surface Variant':                         '#49454E',
    'Outline':                                    '#7A757F',
    'Outline Variant':                            '#CAC4CF',
    'Surface Container Low':                      '#F8F2FA',
    'Surface Container':                          '#F2ECF4',
    'Surface Container High':                     '#ECE6EE',
    'Surface Container Highest':                  '#E6E0E9',
    'Inverse Surface':                            '#322F35',
    'Inverse On Surface':                         '#F5EFF7',
    'Primary Overlay 1':                          'rgba(101, 85, 143, 0.05)',
    'Primary Overlay 2':                          'rgba(101, 85, 143, 0.08)',
    'Primary Overlay 3':                          'rgba(101, 85, 143, 0.12)',
    'On Disabled':                                'rgba(29, 27, 32, 0.38)',
    'Disabled Container':                         'rgba(29, 27, 32, 0.12)',
    'Disabled Container 2':                       'rgba(29, 27, 32, 0.04)',
    'Light Overlay 1':                            'rgba(255, 255, 255, 0.08)',
    'Light Overlay 2':                            'rgba(255, 255, 255, 0.12)',
    'Dark Overlay 1':                             'rgba(74, 68, 88, 0.08)',
    'Dark Overlay 2':                             'rgba(74, 68, 88, 0.12)',
    'Dark Overlay 3':                             'rgba(74, 68, 88, 0.16)',
    'Error Overlay 1':                            'rgba(186, 26, 26, 0.08)',
    'Error Overlay 2':                            'rgba(186, 26, 26, 0.12)',
    'On Surface Variant Overlay 1':               'rgba(73, 69, 78, 0.08)',
    'On Surface Variant Overlay 2':               'rgba(73, 69, 78, 0.12)',
    'Inverse On Surface Overlay 1':               'rgba(245, 239, 247, 0.08)',
    'Inverse On Surface Overlay 2':               'rgba(245, 239, 247, 0.12)',
},
```

**Dark** — contrast: On Primary on Primary 7.7:1; On Surface on Surface 14.3:1; On Primary Container on Primary Container 7.3:1.

```python
'sophisticated_purple_dark': {
    'Primary':                                    '#D0BCFE',
    'Primary Container':                          '#4D3D75',
    'On Primary':                                 '#36265D',
    'On Primary Container':                       '#E9DDFF',
    'Secondary':                                  '#CCC2DB',
    'Secondary Container':                        '#4A4458',
    'On Secondary':                               '#332D41',
    'On Secondary Container':                     '#E8DEF8',
    'Tertiary':                                   '#EFB8C7',
    'Tertiary Container':                         '#633B48',
    'On Tertiary':                                '#4A2532',
    'On Tertiary Container':                      '#FFD9E2',
    'Error':                                      '#FFB4AB',
    'On Error':                                   '#690005',
    'On Error Container':                         '#FFDAD6',
    'Background':                                 '#141218',
    'On Background':                              '#E6E0E9',
    'Surface':                                    '#141218',
    'On Surface':                                 '#E6E0E9',
    'Surface Variant':                            '#49454E',
    'On Surface Variant':                         '#CAC4CF',
    'Outline':                                    '#948F99',
    'Outline Variant':                            '#49454E',
    'Surface Container Low':                      '#1D1B20',
    'Surface Container':                          '#211F24',
    'Surface Container High':                     '#2B292F',
    'Surface Container Highest':                  '#36343A',
    'Inverse Surface':                            '#E6E0E9',
    'Inverse On Surface':                         '#322F35',
    'Primary Overlay 1':                          'rgba(208, 188, 254, 0.05)',
    'Primary Overlay 2':                          'rgba(208, 188, 254, 0.08)',
    'Primary Overlay 3':                          'rgba(208, 188, 254, 0.12)',
    'On Disabled':                                'rgba(230, 224, 233, 0.38)',
    'Disabled Container':                         'rgba(230, 224, 233, 0.12)',
    'Disabled Container 2':                       'rgba(230, 224, 233, 0.04)',
    'Light Overlay 1':                            'rgba(232, 222, 248, 0.08)',
    'Light Overlay 2':                            'rgba(232, 222, 248, 0.12)',
    'Dark Overlay 1':                             'rgba(232, 222, 248, 0.08)',
    'Dark Overlay 2':                             'rgba(232, 222, 248, 0.12)',
    'Dark Overlay 3':                             'rgba(232, 222, 248, 0.16)',
    'Error Overlay 1':                            'rgba(255, 180, 171, 0.08)',
    'Error Overlay 2':                            'rgba(255, 180, 171, 0.12)',
    'On Surface Variant Overlay 1':               'rgba(202, 196, 207, 0.08)',
    'On Surface Variant Overlay 2':               'rgba(202, 196, 207, 0.12)',
    'Inverse On Surface Overlay 1':               'rgba(50, 47, 53, 0.08)',
    'Inverse On Surface Overlay 2':               'rgba(50, 47, 53, 0.12)',
},
```
