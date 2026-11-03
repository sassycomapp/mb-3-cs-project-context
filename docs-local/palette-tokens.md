---
title: "palette-tokens"
doc-id: "mb-8f719d7c"
updated: "2026-11-03"
---
# Mybizz CS — Palette Tokens

This document is the single home of the colour values of the four Mybizz CS palettes, in light and dark. `DESIGN.md` defines how colour is used; this document holds the values.

---

## 1. How the Palettes Are Made

- Each palette is the Material 3 **Tonal Spot** scheme of its locked source colour (Material colour specification 2021, contrast level standard), the same colour system the Anvil Material 3 theme is built on. The source colour is the HCT colour of the palette's hue at chroma 48 and tone 50.
- **Light** values match that scheme within 2 tone steps. Where a darker on-container colour (tone 10) gives higher contrast, the darker colour is used. The surface container levels (Low, Container, High, Highest) step evenly from Surface.
- **Dark** values are the scheme's dark colours, generated from the same source colour.
- Each palette is a complete Anvil Material 3 colour scheme: the 46 colours of the Anvil M3 theme's Colour Scheme, with the same names, in light and in dark.
- Overlay and disabled colours follow the Anvil M3 rules: Primary Overlay 1/2/3 are Primary at 5/8/12%; On Disabled, Disabled Container and Disabled Container 2 are On Surface at 38/12/4%; Error Overlay 1/2 are Error at 8/12%; the On Surface Variant and Inverse On Surface overlays are those colours at 8/12%. In light, Light Overlay 1/2 are white at 8/12% and Dark Overlay 1/2/3 are black at 8/12/16%. In dark, Light Overlay 1/2 are On Primary at 8/12% and Dark Overlay 1/2/3 are On Secondary Container at 8/12/16%.
- Every palette meets WCAG AA contrast for text on its main colours; the measured contrast is stated above each block.
- The values are design constants. They are changed only by regenerating all four palettes from the source colours, never by editing a single value.

### Locked source colours

| Palette | Key | Source colour |
|---|---|---|
| Professional Blue (default) | `professional_blue` | `#5873C4` |
| Warm Coral | `warm_coral` | `#BB5A4F` |
| Calm Teal | `calm_teal` | `#008585` |
| Sophisticated Purple | `sophisticated_purple` | `#8069BF` |

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

- Source colour: `#5873C4`
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
    'Secondary Container':                        '#DEE2FA',
    'On Secondary':                               '#FFFFFF',
    'On Secondary Container':                     '#161B2C',
    'Tertiary':                                   '#745470',
    'Tertiary Container':                         '#FFD6F7',
    'On Tertiary':                                '#FFFFFF',
    'On Tertiary Container':                      '#2B122A',
    'Error':                                      '#BA1A1A',
    'On Error':                                   '#FFFFFF',
    'On Error Container':                         '#410002',
    'Background':                                 '#FAF8FF',
    'On Background':                              '#1A1B20',
    'Surface':                                    '#FAF8FF',
    'On Surface':                                 '#1A1B20',
    'Surface Variant':                            '#E2E2EC',
    'On Surface Variant':                         '#45464F',
    'Outline':                                    '#757680',
    'Outline Variant':                            '#C5C6D0',
    'Surface Container Low':                      '#F4F3F9',
    'Surface Container':                          '#EEEDF4',
    'Surface Container High':                     '#E9E7EF',
    'Surface Container Highest':                  '#E3E2E9',
    'Inverse Surface':                            '#2F3036',
    'Inverse On Surface':                         '#F1F0F7',
    'Primary Overlay 1':                          'rgba(75, 92, 146, 0.05)',
    'Primary Overlay 2':                          'rgba(75, 92, 146, 0.08)',
    'Primary Overlay 3':                          'rgba(75, 92, 146, 0.12)',
    'On Disabled':                                'rgba(26, 27, 32, 0.38)',
    'Disabled Container':                         'rgba(26, 27, 32, 0.12)',
    'Disabled Container 2':                       'rgba(26, 27, 32, 0.04)',
    'Light Overlay 1':                            'rgba(255, 255, 255, 0.08)',
    'Light Overlay 2':                            'rgba(255, 255, 255, 0.12)',
    'Dark Overlay 1':                             'rgba(0, 0, 0, 0.08)',
    'Dark Overlay 2':                             'rgba(0, 0, 0, 0.12)',
    'Dark Overlay 3':                             'rgba(0, 0, 0, 0.16)',
    'Error Overlay 1':                            'rgba(186, 26, 26, 0.08)',
    'Error Overlay 2':                            'rgba(186, 26, 26, 0.12)',
    'On Surface Variant Overlay 1':               'rgba(69, 70, 79, 0.08)',
    'On Surface Variant Overlay 2':               'rgba(69, 70, 79, 0.12)',
    'Inverse On Surface Overlay 1':               'rgba(241, 240, 247, 0.08)',
    'Inverse On Surface Overlay 2':               'rgba(241, 240, 247, 0.12)',
},
```

**Dark** — contrast: On Primary on Primary 7.8:1; On Surface on Surface 14.4:1; On Primary Container on Primary Container 7.2:1.

```python
'professional_blue_dark': {
    'Primary':                                    '#B4C5FF',
    'Primary Container':                          '#334478',
    'On Primary':                                 '#1B2D60',
    'On Primary Container':                       '#DBE1FF',
    'Secondary':                                  '#C1C5DD',
    'Secondary Container':                        '#414659',
    'On Secondary':                               '#2B3042',
    'On Secondary Container':                     '#DDE1F9',
    'Tertiary':                                   '#E2BBDB',
    'Tertiary Container':                         '#5B3D58',
    'On Tertiary':                                '#422740',
    'On Tertiary Container':                      '#FFD6F7',
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
    'Light Overlay 1':                            'rgba(27, 45, 96, 0.08)',
    'Light Overlay 2':                            'rgba(27, 45, 96, 0.12)',
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

- Source colour: `#BB5A4F`
- Best for: salons, spas, lifestyle, beauty.
- Intended effect: warmth, creativity, approachability.

**Light** — contrast: On Primary on Primary 6.5:1; On Surface on Surface 16.2:1; On Primary Container on Primary Container 13.2:1.

```python
'warm_coral': {
    'Primary':                                    '#904A42',
    'Primary Container':                          '#FFDAD5',
    'On Primary':                                 '#FFFFFF',
    'On Primary Container':                       '#3B0A06',
    'Secondary':                                  '#775652',
    'Secondary Container':                        '#FFDAD6',
    'On Secondary':                               '#FFFFFF',
    'On Secondary Container':                     '#2C1512',
    'Tertiary':                                   '#715B2E',
    'Tertiary Container':                         '#FCE0A6',
    'On Tertiary':                                '#FFFFFF',
    'On Tertiary Container':                      '#251A00',
    'Error':                                      '#BA1A1A',
    'On Error':                                   '#FFFFFF',
    'On Error Container':                         '#410002',
    'Background':                                 '#FFF8F7',
    'On Background':                              '#231A19',
    'Surface':                                    '#FFF8F7',
    'On Surface':                                 '#231A19',
    'Surface Variant':                            '#F5DDD9',
    'On Surface Variant':                         '#534341',
    'Outline':                                    '#857370',
    'Outline Variant':                            '#D8C2BE',
    'Surface Container Low':                      '#FFF1F0',
    'Surface Container':                          '#FCEAE7',
    'Surface Container High':                     '#F7E4E2',
    'Surface Container Highest':                  '#F1DEDC',
    'Inverse Surface':                            '#392E2D',
    'Inverse On Surface':                         '#FCEAE8',
    'Primary Overlay 1':                          'rgba(144, 74, 66, 0.05)',
    'Primary Overlay 2':                          'rgba(144, 74, 66, 0.08)',
    'Primary Overlay 3':                          'rgba(144, 74, 66, 0.12)',
    'On Disabled':                                'rgba(35, 26, 25, 0.38)',
    'Disabled Container':                         'rgba(35, 26, 25, 0.12)',
    'Disabled Container 2':                       'rgba(35, 26, 25, 0.04)',
    'Light Overlay 1':                            'rgba(255, 255, 255, 0.08)',
    'Light Overlay 2':                            'rgba(255, 255, 255, 0.12)',
    'Dark Overlay 1':                             'rgba(0, 0, 0, 0.08)',
    'Dark Overlay 2':                             'rgba(0, 0, 0, 0.12)',
    'Dark Overlay 3':                             'rgba(0, 0, 0, 0.16)',
    'Error Overlay 1':                            'rgba(186, 26, 26, 0.08)',
    'Error Overlay 2':                            'rgba(186, 26, 26, 0.12)',
    'On Surface Variant Overlay 1':               'rgba(83, 67, 65, 0.08)',
    'On Surface Variant Overlay 2':               'rgba(83, 67, 65, 0.12)',
    'Inverse On Surface Overlay 1':               'rgba(252, 234, 232, 0.08)',
    'Inverse On Surface Overlay 2':               'rgba(252, 234, 232, 0.12)',
},
```

**Dark** — contrast: On Primary on Primary 7.8:1; On Surface on Surface 14.3:1; On Primary Container on Primary Container 7.2:1.

```python
'warm_coral_dark': {
    'Primary':                                    '#FFB4AA',
    'Primary Container':                          '#73342C',
    'On Primary':                                 '#561E18',
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
    'Inverse On Surface':                         '#392E2C',
    'Primary Overlay 1':                          'rgba(255, 180, 170, 0.05)',
    'Primary Overlay 2':                          'rgba(255, 180, 170, 0.08)',
    'Primary Overlay 3':                          'rgba(255, 180, 170, 0.12)',
    'On Disabled':                                'rgba(241, 222, 220, 0.38)',
    'Disabled Container':                         'rgba(241, 222, 220, 0.12)',
    'Disabled Container 2':                       'rgba(241, 222, 220, 0.04)',
    'Light Overlay 1':                            'rgba(86, 30, 24, 0.08)',
    'Light Overlay 2':                            'rgba(86, 30, 24, 0.12)',
    'Dark Overlay 1':                             'rgba(255, 218, 213, 0.08)',
    'Dark Overlay 2':                             'rgba(255, 218, 213, 0.12)',
    'Dark Overlay 3':                             'rgba(255, 218, 213, 0.16)',
    'Error Overlay 1':                            'rgba(255, 180, 171, 0.08)',
    'Error Overlay 2':                            'rgba(255, 180, 171, 0.12)',
    'On Surface Variant Overlay 1':               'rgba(216, 194, 191, 0.08)',
    'On Surface Variant Overlay 2':               'rgba(216, 194, 191, 0.12)',
    'Inverse On Surface Overlay 1':               'rgba(57, 46, 44, 0.08)',
    'Inverse On Surface Overlay 2':               'rgba(57, 46, 44, 0.12)',
},
```

### Calm Teal

- Source colour: `#008585`
- Best for: fitness, wellness, physiotherapy, health services.
- Intended effect: health, balance, growth.

**Light** — contrast: On Primary on Primary 6.4:1; On Surface on Surface 16.3:1; On Primary Container on Primary Container 13.2:1.

```python
'calm_teal': {
    'Primary':                                    '#006A6A',
    'Primary Container':                          '#9CF1F0',
    'On Primary':                                 '#FFFFFF',
    'On Primary Container':                       '#002020',
    'Secondary':                                  '#4A6363',
    'Secondary Container':                        '#CCE8E7',
    'On Secondary':                               '#FFFFFF',
    'On Secondary Container':                     '#051F1F',
    'Tertiary':                                   '#4B607C',
    'Tertiary Container':                         '#D3E4FF',
    'On Tertiary':                                '#FFFFFF',
    'On Tertiary Container':                      '#041C35',
    'Error':                                      '#BA1A1A',
    'On Error':                                   '#FFFFFF',
    'On Error Container':                         '#410002',
    'Background':                                 '#F4FBFA',
    'On Background':                              '#161D1D',
    'Surface':                                    '#F4FBFA',
    'On Surface':                                 '#161D1D',
    'Surface Variant':                            '#DAE5E3',
    'On Surface Variant':                         '#3F4948',
    'Outline':                                    '#6F7978',
    'Outline Variant':                            '#BEC9C7',
    'Surface Container Low':                      '#EEF4F3',
    'Surface Container':                          '#E9EFEE',
    'Surface Container High':                     '#E3E9E9',
    'Surface Container Highest':                  '#DDE4E3',
    'Inverse Surface':                            '#2B3131',
    'Inverse On Surface':                         '#EDF2F1',
    'Primary Overlay 1':                          'rgba(0, 106, 106, 0.05)',
    'Primary Overlay 2':                          'rgba(0, 106, 106, 0.08)',
    'Primary Overlay 3':                          'rgba(0, 106, 106, 0.12)',
    'On Disabled':                                'rgba(22, 29, 29, 0.38)',
    'Disabled Container':                         'rgba(22, 29, 29, 0.12)',
    'Disabled Container 2':                       'rgba(22, 29, 29, 0.04)',
    'Light Overlay 1':                            'rgba(255, 255, 255, 0.08)',
    'Light Overlay 2':                            'rgba(255, 255, 255, 0.12)',
    'Dark Overlay 1':                             'rgba(0, 0, 0, 0.08)',
    'Dark Overlay 2':                             'rgba(0, 0, 0, 0.12)',
    'Dark Overlay 3':                             'rgba(0, 0, 0, 0.16)',
    'Error Overlay 1':                            'rgba(186, 26, 26, 0.08)',
    'Error Overlay 2':                            'rgba(186, 26, 26, 0.12)',
    'On Surface Variant Overlay 1':               'rgba(63, 73, 72, 0.08)',
    'On Surface Variant Overlay 2':               'rgba(63, 73, 72, 0.12)',
    'Inverse On Surface Overlay 1':               'rgba(237, 242, 241, 0.08)',
    'Inverse On Surface Overlay 2':               'rgba(237, 242, 241, 0.12)',
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
    'Light Overlay 1':                            'rgba(0, 55, 55, 0.08)',
    'Light Overlay 2':                            'rgba(0, 55, 55, 0.12)',
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

- Source colour: `#8069BF`
- Best for: executive coaching, premium consulting, high-end services.
- Intended effect: premium, refined, exclusive.

**Light** — contrast: On Primary on Primary 6.5:1; On Surface on Surface 16.2:1; On Primary Container on Primary Container 13.3:1.

```python
'sophisticated_purple': {
    'Primary':                                    '#65558F',
    'Primary Container':                          '#E9DDFF',
    'On Primary':                                 '#FFFFFF',
    'On Primary Container':                       '#21005D',
    'Secondary':                                  '#625B71',
    'Secondary Container':                        '#E8DEF8',
    'On Secondary':                               '#FFFFFF',
    'On Secondary Container':                     '#1D192B',
    'Tertiary':                                   '#7D5260',
    'Tertiary Container':                         '#FFD8E4',
    'On Tertiary':                                '#FFFFFF',
    'On Tertiary Container':                      '#31111D',
    'Error':                                      '#BA1A1A',
    'On Error':                                   '#FFFFFF',
    'On Error Container':                         '#410002',
    'Background':                                 '#FEF7FF',
    'On Background':                              '#1D1B20',
    'Surface':                                    '#FEF7FF',
    'On Surface':                                 '#1D1B20',
    'Surface Variant':                            '#E7E0EC',
    'On Surface Variant':                         '#49454F',
    'Outline':                                    '#79747E',
    'Outline Variant':                            '#CAC4D0',
    'Surface Container Low':                      '#F7F2FA',
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
    'Dark Overlay 1':                             'rgba(0, 0, 0, 0.08)',
    'Dark Overlay 2':                             'rgba(0, 0, 0, 0.12)',
    'Dark Overlay 3':                             'rgba(0, 0, 0, 0.16)',
    'Error Overlay 1':                            'rgba(186, 26, 26, 0.08)',
    'Error Overlay 2':                            'rgba(186, 26, 26, 0.12)',
    'On Surface Variant Overlay 1':               'rgba(73, 69, 79, 0.08)',
    'On Surface Variant Overlay 2':               'rgba(73, 69, 79, 0.12)',
    'Inverse On Surface Overlay 1':               'rgba(245, 239, 247, 0.08)',
    'Inverse On Surface Overlay 2':               'rgba(245, 239, 247, 0.12)',
},
```

**Dark** — contrast: On Primary on Primary 7.7:1; On Surface on Surface 14.3:1; On Primary Container on Primary Container 7.3:1.

```python
'sophisticated_purple_dark': {
    'Primary':                                    '#CFBDFE',
    'Primary Container':                          '#4C3D75',
    'On Primary':                                 '#36275D',
    'On Primary Container':                       '#E9DDFF',
    'Secondary':                                  '#CBC2DC',
    'Secondary Container':                        '#4A4458',
    'On Secondary':                               '#332D41',
    'On Secondary Container':                     '#E8DEF8',
    'Tertiary':                                   '#EFB8C8',
    'Tertiary Container':                         '#633B48',
    'On Tertiary':                                '#4A2532',
    'On Tertiary Container':                      '#FFD9E3',
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
    'Primary Overlay 1':                          'rgba(207, 189, 254, 0.05)',
    'Primary Overlay 2':                          'rgba(207, 189, 254, 0.08)',
    'Primary Overlay 3':                          'rgba(207, 189, 254, 0.12)',
    'On Disabled':                                'rgba(230, 224, 233, 0.38)',
    'Disabled Container':                         'rgba(230, 224, 233, 0.12)',
    'Disabled Container 2':                       'rgba(230, 224, 233, 0.04)',
    'Light Overlay 1':                            'rgba(54, 39, 93, 0.08)',
    'Light Overlay 2':                            'rgba(54, 39, 93, 0.12)',
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
