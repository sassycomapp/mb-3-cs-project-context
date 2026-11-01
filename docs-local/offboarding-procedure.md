---
title: "offboarding-procedure"
doc-id: "mb-bbb82062"
updated: "2026-11-01"
---
# Mybizz CS — Offboarding Procedure

How a client leaves Mybizz with all of its data, and how Mybizz removes everything it holds for that client.

**Authority:** [[[default:adr-global/adr-client-r2-storage-and-exit-handover|Client R2 Storage and Exit]]](C:/dev/project-library-global/adr-global/adr-client-r2-storage-and-exit-handover.md) ADR, `integration-architecture.md` (Offboarding), [[[default:specifications-global/spec-regulatory-compliance-baseline|Regulatory Compliance Baseline]]](C:/dev/project-library-global/specifications-global/spec-regulatory-compliance-baseline.md). Where a technical fact here differs from `mb-3-cs-architecture-reference.md`, the architecture reference is correct.

---

## 1. When it applies

- The subscription is month to month with no contract. A client can leave at any time; the subscription ends at the end of the month already paid.
- The Owner asks to leave by a support ticket (F-32) or by email to Mybizz. Only the Owner can ask.
- Mybizz confirms the request with the Owner by email before any step below.

## 2. What the client keeps

The client's Cloudflare account and its R2 bucket are already the client's: the account is in the client's own name, on the client's own email address, and Mybizz is only an invited member. Nothing is transferred. At exit the bucket holds:

| Folder | Content |
|---|---|
| `exports/` | The full export written at exit (section 3) |
| `backups/daily/`, `backups/weekly/` | The instance backups |
| `history/{table}/` | Archived rows |
| `audit/{year}/{month}/` | The archived `activity_log` |
| `webhooks/{service}/{year}/{month}/` | Raw webhook payloads |
| `email/messages/`, `email/attachments/` | Email bodies and attachments moved out of the instance |
| `expenses/receipts/` | Expense receipts (F-28) |

The client's domain is also the client's own, registered by the client.

## 3. Steps

| # | Step | Done by |
|---|---|---|
| 1 | Confirm the request with the Owner by email; record it in App 5 against the client | Mybizz |
| 2 | Run the exit export from App 5. The instance writes into `exports/`: every data table as CSV, every email still in the instance with its attachments, the accounting export in Wave, Xero and QuickBooks formats (F-28), the instance's `encryption_key`, and the Vault's contents in decrypted form | App 5 and the instance |
| 3 | The Owner opens the bucket in the Cloudflare dashboard and confirms by email that the export is there and opens | Owner |
| 4 | Mybizz's Cloudflare user leaves the client's Cloudflare account. This ends Mybizz's bucket token and its DNS access. The account and everything in it stay the client's | Mybizz |
| 5 | App 5 removes the SES DNS records from the client's domain if the Owner asks (DKIM, MAIL FROM, SPF, DMARC, MX), before step 4; otherwise the Owner removes them | App 5 or Owner |
| 6 | App 5 deletes the client's SES tenant, domain identity, receipt rule, SNS topics and AWS keys, and empties the client's S3 prefix | App 5 |
| 7 | The client's domain is disconnected from the instance; the instance is deleted and its Mybizz-owned Anvil Business account is closed | Mybizz |
| 8 | App 5 marks the client as left and keeps only the records Mybizz must keep by law (invoices and payments from Mybizz to the client) | App 5 |
| 9 | Mybizz emails the Owner that offboarding is complete | Mybizz |

## 4. Rules

- The export (step 2) is never skipped and is confirmed (step 3) before anything is deleted.
- Mybizz keeps none of the client's data after step 8.
- The decrypted Vault holds live credentials. The Owner is told in the step 9 email to change the gateway keys (Stripe, Paystack, PayPal) once the instance is closed.
- The exit export is proven on the private test client before the first release.

---

*End of file — Offboarding Procedure*
