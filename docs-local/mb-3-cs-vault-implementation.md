---
title: "mb-3-cs-vault-implementation"
doc-id: "mb-fc24acf4"
updated: "2026-11-02"
---
# Mybizz CS — Vault Implementation

**Scope:** `mb-3-cs` only. The Vault pattern every Mybizz application follows is [[[default:specifications-global/spec-vault-system]]](C:/dev/project-library-global/specifications-global/spec-vault-system.md); this document holds what `mb-3-cs` stores in its Vault, its VaultForm and its build sequence.

## 1. What's Stored in the Vault

| Secret | Used by |
|---|---|
| `stripe_secret_key`, `stripe_webhook_secret` | Payment Gateway (Stripe) |
| `paystack_secret_key` | Payment Gateway (Paystack) |
| `paypal_client_secret`, `paypal_webhook_id` | Payment Gateway (PayPal) |
| `aws_access_key_id`, `aws_secret_access_key` | Amazon SES sending and the client's received-email folder in S3 (`team-inbox-spec.md`); written by App 5 |
| `ses_smtp_password` | Anvil's Email service, Custom Mail Server on the SES SMTP endpoint; written by App 5 |
| `r2_access_key_id`, `r2_secret_access_key` | The client's R2 bucket; written by App 5 |
| `app5_bearer_token` | Calls between the instance and App 5 |
| `vault_totp_secret` | The Vault's authenticator-app code |

Publishable keys (e.g. Stripe publishable key) are safe for client-side use and are stored in the `payment_config` table, not the Vault.

**SMTP constants** (identical for all clients, not stored in the Vault):
- Host: `email-smtp.eu-west-1.amazonaws.com`
- Port: `587` (STARTTLS)

## 2. VaultForm UI

Standalone form accessed from Settings, visible to Owner only:
- Second-factor notice, displayed prominently at the top: every open needs a sign-in with the Owner's second factor in the last five minutes and a code from the Owner's authenticator app (TOTP), with no remembered session.
- Reset Vault code: emails the Owner a single-use link, valid for 10 minutes, which clears `vault_totp_secret`; the next open enrols a new authenticator (`vault-recovery-procedure.md`).
- Stored Secrets list — name, last updated date, usage context. View (masked reveal) and Delete actions per item.
- Add New Secret form — name + password input (`hide_text=True`) at the bottom. Value never displayed again after saving.

Visual references: `wireframes/wireframe-settings-VaultForm.html`, `screens/screen-settings-VaultForm.html`.

## 3. Build Sequence

Built after authentication and settings, before payments, bookings and services, which depend on it.

### Deliverables
- `vault` Data Table with encrypted secrets storage
- `encryption_key` set in Anvil Secrets
- `server_shared/encryption_service.py`
- `server_shared/vault_service.py`
- `server_shared/vault_step_up_service.py`
- `settings/VaultForm`

### Completion Criteria
- `encryption_key` set in Anvil Secrets (only item there)
- VaultForm requires a fresh authenticator code on every open, no grace period
- First open enrols the Owner's authenticator (QR code and one confirming code); Reset Vault code clears `vault_totp_secret` through the emailed link (`vault-recovery-procedure.md`)
- Email notification sent to Owner on every Vault access
- Secret keys stored encrypted in `vault` table
- `get_vault_secret()` retrieves secrets correctly
- Payment config uses Vault for secret keys (not stored in `payment_config` table)
- Secret masking in `get_payment_config()` returns `'***'`
