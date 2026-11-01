---
title: "team-inbox-spec"
doc-id: "mb-17a38acb"
updated: "2026-11-01"
---
# Mybizz CS — Team Inbox and Email Specification

## 1. What the Team Inbox is

The Team Inbox gives every client of Mybizz CS email on the client's own domain, inside the client's Mybizz app. The Owner creates as many addresses as the business needs: personal addresses (`john@abc.com`) and desk addresses (`help@abc.com`, `marketing@abc.com`, `accounts@abc.com`). Every email to any of those addresses arrives in the Team Inbox; every email the business sends, from a one-to-one reply to a newsletter, leaves through the same email service under the client's own domain.

There is no limit on the number of addresses, no mailbox to buy and no email bill for the client. The client's email stays inside the client's own app, linked to the client's CRM, and leaves with the client's data at exit.

## 2. One email service: Amazon SES

Every email of Mybizz CS, received or sent, goes through Amazon SES (Simple Email Service), in Mybizz's AWS account, in the region `eu-west-1` (Ireland).

| Email | How it travels |
|---|---|
| Received, to any address on the client's domain | SES receives it, scans it for spam and viruses, and passes it to the client's instance |
| One-to-one emails and replies from the Team Inbox | SES, through the instance's email service |
| Confirmations, reminders, invoices, receipts | SES, through the instance's email service |
| Newsletters, campaigns and sequences | SES, through the instance's email service |
| Sign-in, confirmation and password-reset emails of Anvil's Users service | SES, through Anvil's Email service set to Custom Mail Server with the SES SMTP endpoint |

One service for every email means one sending reputation to look after, one set of DNS records per domain and one place where bounces, complaints, opens and clicks are reported. Anvil's own email service is not used to send, and Anvil's email quota does not apply.

Each client is one SES tenant. SES keeps each tenant's sending reputation separate, so one client's mistakes never harm another client's delivery.

**Cost:** $0.10 per 1,000 emails sent and per 1,000 emails received, absorbed by Mybizz.

## 3. The client's domain

The onboarding wizard's step "Connect the client's domain" sets up the domain for the website and for email in one pass. The client's domain DNS is held in the client's Cloudflare account, the same account that holds the client's R2 storage. The client invites Mybizz's Cloudflare user with the roles Cloudflare R2 Admin and DNS. App 5 then writes every record through the Cloudflare API and checks each until it is live.

| Record | Name | Value | Purpose |
|---|---|---|---|
| A | `@` (and `www`) | Anvil's address for custom domains | The website |
| MX | `@` | `inbound-smtp.eu-west-1.amazonaws.com`, priority 10 | Email to the domain is delivered to SES |
| CNAME × 3 | `{token}._domainkey` | Values issued by SES for the domain | DKIM: SES signs every email, proving it is genuine |
| MX | `mail` | `feedback-smtp.eu-west-1.amazonses.com`, priority 10 | The MAIL FROM domain `mail.abc.com`, which receives bounce reports |
| TXT | `mail` | `v=spf1 include:amazonses.com ~all` | SPF: SES is allowed to send for the domain |
| TXT | `_dmarc` | `v=DMARC1; p=quarantine; adkim=r; aspf=r` | DMARC: receivers treat unsigned email claiming to be from the domain as suspect |

A domain whose DNS is held elsewhere receives the same records from the wizard as a list to copy into the domain provider's settings; the wizard checks them every few minutes and confirms when all are live.

A domain that already receives email elsewhere (for example in Google Workspace) keeps working until its MX record changes. The wizard states plainly that switching the MX record moves all email for the domain into the Team Inbox, and asks the Owner to confirm before it writes the record.

## 4. Addresses

Owners and Managers create and manage addresses in Settings → Email.

| Field | Meaning |
|---|---|
| Address | The part before the `@`, on the client's domain |
| Kind | `personal` (one staff member) or `desk` (shared by a team) |
| Display name | The sender name shown to recipients: "John Smith", "ABC Help Desk" |
| Members | The staff members who read and send from the address; a personal address has one |
| Signature | Added to every email sent from the address |
| Auto-reply | Optional, with a start and end date |

Every staff member sees the addresses of which the staff member is a member, and nothing else. Owners see every address. Customers have no Team Inbox.

An email to an address on the domain that does not exist lands in **Unassigned**, which Owners and Managers see. From there it is moved to an address, deleted, or the address is created.

## 5. Receiving

1. SES receives the email for the client's domain, checks it for spam and viruses, checks its SPF, DKIM and DMARC, and stores the raw email in Mybizz's S3 bucket under the client's prefix.
2. SES notifies the client's instance through its SNS topic, which calls the instance's endpoint `/_/api/email/inbound`.
3. The endpoint verifies the SNS signature, and dispatches a background task.
4. The background task reads the raw email from S3, parses it with Python's `email` library, and writes one row to `email_messages`: the address it was sent to, the sender, the recipients, the subject, the text and HTML bodies, the threading headers and the SES verdicts. Attachments go to the client's R2 bucket. The raw email is deleted from S3.
5. A sender who matches a contact's email links the message to that contact, and the email appears on the contact's activity timeline.

An email whose virus verdict fails is deleted unread and never stored. An email whose spam or DMARC verdict fails goes to the **Spam** folder; Spam is emptied after 30 days.

The instance's AWS keys allow reading and deleting only under the client's own S3 prefix, and sending only from the client's own domain. App 5 creates the keys at provisioning; the keys are held in the client's Vault.

## 6. The Team Inbox screen

`TeamInboxForm`, in the admin area:

- **Left:** the user's addresses, each with its unread count, and the folders Inbox, Drafts, Sent, Archive, Spam and Trash; Unassigned for Owners and Managers.
- **Middle:** the messages of the selected address and folder: sender or recipient, subject, first line, date, unread mark, star, attachment mark.
  - **Sort** by date (newest or oldest first), sender, recipient or subject.
  - **Filter** by unread, starred, with attachments, date range, and sender.
  - **Search** across sender, recipient, subject, body and attachment names, in one address or across all the user's addresses.
  - **Select** one or many messages, then mark as read or unread, star, archive, move to another address, move to Spam or delete.
- **Right:** the open message and its whole conversation, with Reply, Reply All, Forward, Star, Mark as unread, Archive, Delete, Move to address, Download attachment, and **Add as lead** when the sender is not yet a contact. A message from a contact shows a link to the contact's record.
- **Compose:** To, Cc, Bcc (with contacts suggested as the user types), subject, rich text body, attachments, the address to send from, and the address's signature. A message being written is saved to Drafts every few seconds. The AI helper's email response assist (AI assist 6) drafts a reply on request, in the client's app language.
- **Trash** keeps deleted messages for 30 days, then deletes them for good; Restore returns a message to its folder.

Messages are grouped into conversations by their `Message-ID`, `In-Reply-To` and `References` headers. Read and unread is shared per address, so a desk sees which emails a colleague has already opened. The menu shows the user's total unread count.

The Team Inbox works in the phone browser, from the same home-screen shortcut as WhatsUp voice.

## 7. Sending

Every email is sent by `email_service` in `server_shared/` through the SES API, from an address on the client's domain, signed with the domain's DKIM, in the client's SES tenant and configuration set. A sent email from the Team Inbox is saved in `email_messages` with the folder Sent.

SES reports every bounce, complaint, delivery, open and click to the instance's endpoint `/_/api/email/events`. A hard bounce or a complaint puts the address on `email_suppressions`, and no email of any kind is sent to a suppressed address again; a contact who unsubscribes is suppressed for marketing email only.

## 8. Newsletters and campaigns

Campaigns, broadcasts and sequences (F-18) are written in the email editor and sent through SES like every other email:

- Each recipient receives the email individually, in batches that stay within the SES sending rate.
- Every marketing email carries an unsubscribe link and the `List-Unsubscribe` and `List-Unsubscribe-Post` headers, so mail providers show a one-click unsubscribe; the link and the one-click request are handled by the endpoint `/_/api/email/unsubscribe`.
- SES's open and click tracking records opens and clicks per recipient into `campaign_metrics`.
- Marketing email is sent only to contacts who opted in and are not suppressed.

## 9. Storage

Every email stays in `email_messages` for 90 days. After 90 days, a nightly background task writes the email's bodies to the client's R2 bucket and keeps the row as an index (address, sender, subject, date, `r2_key`), so the email stays searchable and opens from R2. Attachments are always in R2. The client's email is part of the data export at exit.

## 10. Data

| Table | Change |
|---|---|
| `email_addresses` | New: `address`, `kind` (`personal` / `desk`), `display_name`, `member_ids` (links → users), `signature`, `auto_reply_text`, `auto_reply_from`, `auto_reply_until`, `is_active` |
| `email_messages` | New: `address_id` (link → email_addresses; empty for Unassigned), `folder` (`inbox` / `drafts` / `sent` / `archive` / `spam` / `trash`), `direction` (`incoming` / `outgoing`), `from_address`, `from_name`, `to_addresses`, `cc_addresses` (simpleObject), `subject`, `body_text`, `body_html`, `attachments` (simpleObject: R2 keys, names, sizes), `message_id`, `in_reply_to`, `thread_id`, `contact_id` (link → contacts), `is_read`, `is_starred`, `deleted_at`, `spam_verdict`, `virus_verdict`, `dmarc_verdict`, `r2_key`, `received_at`, `sent_at` |
| `email_suppressions` | New: `email`, `reason` (`bounce` / `complaint` / `unsubscribe`), `scope` (`all` / `marketing`), `created_at` |
| `email_config` | Replaces the Brevo settings: `domain`, `ses_tenant`, `dns_verified`, `dkim_verified`, `mail_from_verified`, `mx_verified`, `verified_at` |
| `email_log` | `brevo_message_id` becomes `ses_message_id` |
| `webhook_log` | `gateway` adds `ses` for the SES event and inbound notifications |

## 11. Server modules and endpoints

| Item | Package | Purpose |
|---|---|---|
| `email_service` | `server_shared/` | Every outgoing email, through SES |
| `inbox_service` | `server_inbox/` (new) | Addresses, folders, conversations, search, Add as lead |
| `inbound_tasks` | `server_inbox/` | Reading, parsing and storing received email; the 90-day archive to R2 |
| `/_/api/email/inbound` | `server_inbox/` | SNS notification of a received email |
| `/_/api/email/events` | `server_inbox/` | SES bounce, complaint, delivery, open and click events |
| `/_/api/email/unsubscribe` | `server_marketing/` | The unsubscribe link and one-click unsubscribe |

App 5 holds the SES account, creates each client's SES tenant, domain identity, receipt rule, SNS topics and scoped AWS keys at provisioning, and writes the client's DNS records (section 3).

## 12. What Mybizz sets up once

- The AWS account, with SES production access granted by Amazon.
- The SES receipt rule set and the S3 bucket for received email in `eu-west-1`.
- Mybizz's Cloudflare user, which clients invite.

## 13. Related documents

| Document | What it holds for this feature |
|---|---|
| `authoritative-schema.md` | The tables of section 10 |
| `integration-architecture.md` | SES alongside the payment gateways, R2 and Wave |
| `scaffold-spec.md` | `server_inbox/`, `email_service`, the endpoints |
| `DESIGN.md` | TeamInboxForm and Settings → Email |
| `user-flows.md` | Reading, replying, managing addresses, connecting the domain |
| `mybizz-ai-implementation-spec.md` | AI assist 6, email response suggestions |
| `mb-3-cs-architecture-reference.md` | F-18 Email |
