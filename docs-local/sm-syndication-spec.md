---
title: "sm-syndication-spec"
doc-id: "mb-620f6900"
updated: "2026-11-01"
---
# Mybizz CS — Social Media Syndication Specification

## 1. What social media syndication is

Social media syndication lets a client share each published blog post to Facebook, LinkedIn, WhatsApp and Instagram in one tap, with a caption written by AI in the client's app language and a full preview of the post.

The client shares from the client's own social accounts, signed in on the client's own device. Mybizz CS holds no social media password, key or token, and needs no approval from Meta, LinkedIn or WhatsApp. Every share uses each platform's own public share link or the device's own share menu.

The design rests on four requirements: simple, secure, robust and fit for purpose.

- **Simple:** one Share button, and one tap per platform.
- **Secure:** no social media credential is ever stored, so none can be lost or misused.
- **Robust:** public share links and the device share menu need no approval from Meta, LinkedIn or WhatsApp, and are not withdrawn when a platform changes its developer rules.
- **Fit for purpose:** every share shows the post's title, image and summary, and links back to the client's own website.

## 2. Who uses it

Owner and Manager, the two roles with access to BlogEditorForm. Staff, Admin and Customer do not see the Share panel.

## 3. How a client shares a post

1. The client publishes a blog post in BlogEditorForm (Publish Now), or a scheduled post reaches its publish time.
2. On publishing, Mybizz CS asks the AI helper for one caption per platform. The captions are stored on the post.
3. A Share button appears in BlogEditorForm and in the blog post list for every published post.
4. Share opens the Share panel. The panel shows the post's preview (featured image, title, excerpt) and one editable caption per platform, with a button for each platform.
5. Tapping a platform button copies that platform's caption and opens the platform:

| Platform | What opens | What the client does |
|---|---|---|
| Facebook | Facebook's share window, with the post's link and preview | Pastes the caption and posts |
| LinkedIn | LinkedIn's share window, with the post's link and preview | Pastes the caption and posts |
| WhatsApp | WhatsApp, with the caption and the link already filled in | Picks a contact, a group or Status, and sends |
| Instagram | The phone's share menu, with the post's featured image | Picks Instagram, pastes the caption and posts |

6. The panel confirms each copy with a short message: "Caption copied — paste it into your post."

Instagram accepts no shared website links, so Instagram is shared as an image with a caption. The Instagram caption ends with "Link in bio" and the post's address in plain text. On a computer, the Instagram button downloads the featured image and copies the caption, and the panel says: "Open Instagram on your phone and post this image with the caption."

## 4. The AI captions

The captions are written by a new assist in the shared AI helper: the social caption assist, specified in `mybizz-ai-implementation-spec.md`. It runs through App 5, as every AI request does, at no cost to the client.

| Platform | Caption |
|---|---|
| Facebook | 2 to 3 sentences, friendly, with a call to read the post |
| LinkedIn | 3 to 5 sentences, professional, with one key point from the post and a call to read it |
| WhatsApp | 1 to 2 sentences, personal, followed by the post's link |
| Instagram | 2 to 4 sentences, with 3 to 5 relevant hashtags, ending with "Link in bio" and the post's address |

The assist reads the post's title, excerpt, content, tags and category, and writes in the app language set in `business_profile`. The client can edit any caption in the Share panel before sharing. An edited caption is saved on the post. Regenerate asks the assist for a new set.

If the AI helper is unavailable, each caption is filled with the post's title and excerpt, so sharing still works.

## 5. The preview every share shows

Facebook, LinkedIn and WhatsApp build the preview from tags in the page that the post's link opens. Every public blog post route sets those tags on the server for the first request, through the Routing dependency's `Route.meta` method, which hands them to Anvil's `AppResponder`. Social platforms read only the first response, so the tags must be set there.

| Tag | Value |
|---|---|
| `title` | `meta_title`, or `title` when `meta_title` is empty |
| `description` | `meta_description`, or `excerpt` when `meta_description` is empty |
| `og:title` | As `title` |
| `og:description` | As `description` |
| `og:image` | The featured image's public address (below) |
| `og:url` | The post's address on the client's own domain |
| `og:type` | `article` |
| `twitter:card` | `summary_large_image` |

A Data Table media file has no permanent public address, so a server HTTP endpoint serves each published post's featured image at `/_/api/blog-image/{slug}`. The endpoint returns the image of a published post only; any other request returns 404. When a post has no featured image, the client's `business_logo` is served.

## 6. Where the traffic comes from

Every shared link carries the platform's name: `?ref=facebook`, `?ref=linkedin`, `?ref=whatsapp` or `?ref=instagram`. When a visitor arrives through a shared link and then books, enquires or fills in a form, the new contact's `source` is recorded as `social`, and the platform is kept in the contact's activity timeline. The dashboard shows visits and new contacts per platform for each post.

## 7. Readers sharing a post

The public blog post page carries share buttons for readers: Facebook, LinkedIn, WhatsApp and Copy link. Reader shares use the same share links and previews, with no caption. Reader shares carry the same `?ref=` values.

## 8. Data

`blog_posts` gains one column:

| Column | Type | Notes |
|---|---|---|
| `share_captions` | simpleObject | One caption per platform: `facebook`, `linkedin`, `whatsapp`, `instagram`; written by the social caption assist, editable by the client |

No other table changes. No social media credential is stored anywhere.

## 9. How the platforms are opened

| Platform | Address opened |
|---|---|
| Facebook | `https://www.facebook.com/sharer/sharer.php?u={post address}` |
| LinkedIn | `https://www.linkedin.com/sharing/share-offsite/?url={post address}` |
| WhatsApp | `https://wa.me/?text={caption and post address}` |
| Instagram | The device share menu (`navigator.share`) with the featured image as a file and the caption as text |

Each address opens in a new browser tab. The caption is copied with `navigator.clipboard.writeText`, called from client code through `anvil.js`. Facebook and LinkedIn take only the link, because both platforms ignore text supplied by another site; the client pastes the copied caption.

## 10. Help

The Share panel's tooltip opens a short help video showing one share to each platform, in the client's app language.

## 11. Related documents

| Document | What it holds for this feature |
|---|---|
| `mybizz-ai-implementation-spec.md` | The social caption assist |
| `authoritative-schema.md` | `blog_posts.share_captions`; `contacts.source` |
| `DESIGN.md` | The Share panel and the reader share buttons |
| `user-flows.md` | UF-16, publishing and sharing a blog post |
| `mb-3-cs-architecture-reference.md` | F-21 Blog and content |
