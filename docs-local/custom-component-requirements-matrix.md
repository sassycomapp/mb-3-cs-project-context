---
title: "custom-component-requirements-matrix"
doc-id: "mb-680c4ac6"
updated: "2026-11-01"
---
# Mybizz CS — Custom Component Requirements Matrix

Every custom component Mybizz CS needs, where each one is used, and why no native Anvil Material 3 component serves. This document is the single home of the component list; `DESIGN.md` defines the tokens, typography and roles every component uses. Feature numbers (F-01 to F-36) are those of `mb-3-cs-concept.md`.

---

## Definition

A custom component is a reusable interactive UI element that:

1. Is not provided, or not adequately provided, by the Anvil Material 3 theme ([[[default:specifications-global/spec-m3_component_mapping|M3 Component Mapping]]](C:/dev/project-library-global/specifications-global/spec-m3_component_mapping.md)); and
2. Is used in more than one place.

An element that meets rule 1 but is used in only one place is a **local component**: it lives in the folder of the one form that uses it.

Page layouts (homepage, blog and landing page templates) are forms, not custom components; they are listed in `DESIGN.md` under Template and Layout Architecture. The booking calendar is a feature module, specified in `calendar-feature-spec.md`.

All custom components:
- live in the `client_code/components/` package;
- use only the colour tokens, typography, spacing and roles in `DESIGN.md` — no hardcoded colours;
- use only components listed in the M3 Component Mapping, plus `anvil.js` where a browser capability is required (never `eval`);
- follow [[[default:specifications-global/spec-nomenclature|Nomenclature]]](C:/dev/project-library-global/specifications-global/spec-nomenclature.md) for their inner component names.

---

## Custom Component Matrix

### Shared custom components (11)

| Component | Feature | Used in | Uses | Why no native component serves |
|---|---|---|---|---|
| VideoDisplayComponent | F-09, F-25 | HomePage, services page, ServiceViewerForm, blog posts, HelpTooltipComponent | 5 | The M3 theme has no video embed; the native YouTube component handles one source only |
| FooterComponent | F-06 | Every public page (HomePage, About, Contact, Services, Blog listing, Blog post, Privacy Policy, Terms and Conditions) | 8 | No footer component; the footer combines logo, credentials, social links, policy links, map and contact |
| TimeLapseCarouselComponent | F-09 | HomePage, services page | 2 | No carousel component |
| ParallaxComponent | F-09 | HomePage hero, About, Services, landing pages | 4 | No parallax effect |
| ClauseBuilder | F-22 | PolicyEditorForm for the Privacy Policy and for Terms and Conditions | 2 | No clause-level document editor |
| AIHelperComponent | F-26, F-27 | Business Profile, ServiceEditorForm, FAQ editor, proposal editor, QuoteEditorForm, BlogEditorForm, Team Inbox compose, InvoiceEditorForm, Clause Builder explainer, ContactViewerForm, BookingCalendar insights, EditModeComponent, SharePanelComponent | 13 | No floating AI assistant with assist selection, voice input and spoken output |
| StarRatingComponent | F-23 | Customer review form, ReviewViewerForm, homepage testimonials, customer portal My Reviews | 4 | No star rating input or display |
| HelpTooltipComponent | F-25 | Every feature on every admin screen, every onboarding step | many | The native `tooltip` property shows text only; no tooltip opens a video pop-up |
| EditModeComponent | F-06, F-08, F-21 | Every public page, every landing page template, every blog post template | 15 | No in-place editing of a published page |
| SharePanelComponent | F-21 | BlogEditorForm, BlogListForm | 2 | No share panel with captions and platform share links |
| ReaderShareComponent | F-21 | BlogPostClassicTemplate, BlogPostTwoColumnTemplate, BlogPostFullWidthTemplate | 3 | No social share buttons |

### Local components (1)

| Component | Feature | Used in | Reason it is local |
|---|---|---|---|
| PaletteSettingsForm | F-31 | Settings → Brand Palette | Used in one place only; onboarding links to the Settings tab instead of embedding it |

### Not built

| Component | Reason |
|---|---|
| AnimatedButtonComponent, AnimatedCardComponent, AnimatedLinkComponent, AnimatedImageComponent | Native M3 components provide the required hover and focus feedback through roles |
| OnboardingPaletteForm | Onboarding links to Settings → Brand Palette |
| Chip components | The M3 theme has no native chip; status and filter needs are met with outlined status badges and buttons (`DESIGN.md`, Status Badges) |

---

## Implementation Notes

### VideoDisplayComponent

- **Display only:** no source selector or address input inside the component. Address inputs belong to the host forms (BlogEditorForm, ServiceEditorForm, Settings → Business Profile).
- **Properties:** `video_url` and `video_type`, set in code by the host form.
- **Sources:** YouTube, Vimeo, Dailymotion and direct video addresses; `video_type` ∈ `youtube`, `vimeo`, `dailymotion`, `direct`, detected from the address when the address is saved.
- **Visibility:** set in code — an empty or invalid address hides the component. No user-facing show/hide switch.
- **Host contexts and address sources:**

| Host context | Video address source | Governed by |
|---|---|---|
| Services page | `business_profile.services_video_url` | Settings → Business Profile |
| HomePage | `business_profile.home_video_url` | Settings → Business Profile |
| Blog post (public view) | `blog_posts.video_url` | BlogEditorForm → Video Embed card |
| ServiceViewerForm | `services.video_url` | ServiceEditorForm → video address field |
| Process-flow help video | help content record | Mybizz, through `master_template` |

### FooterComponent

- Contains: business logo, accreditations, social links, contact email and phone, policy links (Privacy Policy, Terms and Conditions), optional Google Maps embed.
- Reads its content from `business_profile`; the map shows only when `google_maps_embed_url` is set.
- Identical on every public page.

### TimeLapseCarouselComponent

- Auto-rotating carousel with previous and next controls and dot indicators.
- Pauses on hover and on keyboard focus; rotation stops when the user navigates manually.
- Items are supplied by the host form (active services, featured content or testimonials).
- Hidden when it has no items.

### ParallaxComponent

- Background image moving at a different speed from the foreground on scroll.
- Implemented with CSS and minimal `anvil.js`; disabled when the device requests reduced motion.
- Text over the image keeps WCAG AA contrast through a token-derived overlay.

### ClauseBuilder

- Parameterised by document type: `privacy_policy` or `t_and_c`.
- Supports category selection, seeded clauses (editable, not deletable), custom clauses (editable and deletable), clause editing, drag reordering and switching clauses on or off.
- Data model: `category`, `title`, `clause`, `sort_order`, `is_seeded`, `is_active`.
- On save: writes to the `privacy_policy` or `t_and_c` table and publishes the public page immediately.
- Hosted by PolicyEditorForm in Settings → Legal Pages.

### AIHelperComponent

- Specified in full in `mybizz-ai-implementation-spec.md`.
- Trigger button placed on the host form; floating window over the current page with assist selector, working area and result area.
- The host form passes its context; the component preselects the matching assist.
- Result actions: copy, insert, replace, append, cancel.
- Microphone input (speech-to-text through the server) and spoken output (browser speech synthesis, or Groq TTS where the natural-sounding voice is on).
- Every request goes through the shared AI request layer in `server_ai/`, routed through App 5.
- On failure, shows a clear message and leaves the host form working.

### HelpTooltipComponent

- A small help icon button placed beside a feature's heading or field; its accessible label is the feature's plain-language tooltip text.
- Pressing it opens a pop-up with the tooltip text and the feature's help video (VideoDisplayComponent) in the app language; Close returns focus to the icon.
- Properties: `help_key` (the feature's help entry), set in code. Hidden when the user has switched tooltips off (`users.preferences.tooltips`).
- Help entries (text and video address per language) are held in `master_template`, so every client receives new and corrected videos.

### EditModeComponent

- Placed once on each public page form, landing page template and blog post template; visible only to a signed-in Owner or Manager.
- Shows the Edit button; in edit mode marks every element whose name is registered as editable, edits it in place (text, image upload, video address), offers the AI helper for text, and saves every change in one server call (`DESIGN.md`, Edit Mode).
- The host form registers its editable elements and the content record they belong to (`pages.components`, `config.home_config`, `landing_pages.config`, `blog_posts.content`).

### SharePanelComponent

- Specified in `sm-syndication-spec.md`; design in `DESIGN.md`, Share Panel.
- Property: the blog post row; reads and saves `blog_posts.share_captions`; copies with `navigator.clipboard.writeText` and opens platform share links or `navigator.share` through `anvil.js`.

### ReaderShareComponent

- Icon buttons for Facebook, LinkedIn, WhatsApp and Copy link; each shared link carries `?ref=` (`sm-syndication-spec.md`).

### StarRatingComponent

- Input mode: one to five stars, selectable by pointer and keyboard, with an accessible label for each value.
- Display mode: read-only stars with the rating also given as text ("4 out of 5").
- Colours from tokens only: filled stars Primary, empty stars Outline.

### PaletteSettingsForm (local)

- Shows each of the four palettes with a primary swatch, a sample filled and outlined button, a sample card, a sample heading and link, and the target business fit.
- Choosing a palette previews it live across the screen through the palette module (`palette-tokens.md`); Save stores it in `business_profile.palette`.
- Lives in the folder of SettingsForm.
