---
title: "vault-recovery-procedure"
doc-id: "mb-fc887091"
updated: "2026-11-01"
---
# Mybizz CS — Vault Recovery Procedure

How the Owner gets back into the Vault after losing the authenticator app that gives its code.

**Authority:** `mb-3-cs-vault-implementation.md`, `mb-3-cs-architecture-reference.md` (F-02), [[[default:specifications-global/spec-vault-system|The Vault System]]](C:/dev/project-library-global/specifications-global/spec-vault-system.md). Where a technical fact here differs from `mb-3-cs-architecture-reference.md`, the architecture reference is correct.

---

## 1. When it applies

- The Vault opens only with a code from the Owner's authenticator app (TOTP, `vault_totp_secret`), on every view, with no remembered session (F-02).
- This procedure applies when the Owner has lost that authenticator: a lost or reset phone, or a deleted app entry.
- There is no self-service recovery and no backup code. Only Mybizz can reset the Vault code.
- A Vault locked after 5 failed codes is not a recovery case: it opens again after 15 minutes.

## 2. Steps

| # | Step | Done by |
|---|---|---|
| 1 | The Owner opens a support ticket (F-32) while signed in to the instance, asking for a Vault code reset | Owner |
| 2 | Mybizz confirms the Owner's identity by a separate channel: a phone call to the Owner's number on the client's App 5 record, where the Owner gives the client reference number. A request that fails this check is refused | Mybizz |
| 3 | Mybizz resets the code from App 5 through the instance's management endpoint, with the instance's Bearer token. The endpoint clears `vault_totp_secret`. No stored secret is read or changed | Mybizz, App 5 |
| 4 | On the next open of the Vault, the instance asks the Owner to enrol a new authenticator: it shows a QR code, the Owner scans it and enters one code to confirm | Owner |
| 5 | The reset and the enrolment are written to `activity_log` and to App 5 against the client | The instance, App 5 |
| 6 | The instance emails the Owner that the Vault code was reset | The instance |

## 3. Rules

- The reset clears only the code. The Vault's secrets stay encrypted with the instance's `encryption_key` and are untouched.
- Only the Owner's role can enrol a new authenticator.
- Mybizz never sees, sets or sends a Vault code.

---

*End of file — Vault Recovery Procedure*
