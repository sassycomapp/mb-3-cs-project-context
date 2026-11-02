---
title: "vault-recovery-procedure"
doc-id: "mb-fc887091"
updated: "2026-11-02"
---
# Mybizz CS — Vault Recovery Procedure

How a user gets back in after losing a second factor: the sign-in second factor of any user, or the Owner's Vault authenticator. Both are self-service; Mybizz takes no part.

**Authority:** `mb-3-cs-vault-implementation.md`, `mb-3-cs-architecture-reference.md` (F-01, F-02), [[[default:specifications-global/spec-security|Security Specification]]](C:/dev/project-library-global/specifications-global/spec-security.md), [[[default:specifications-global/spec-vault-system|The Vault System]]](C:/dev/project-library-global/specifications-global/spec-vault-system.md). Where a technical fact here differs from `mb-3-cs-architecture-reference.md`, the architecture reference is correct.

---

## 1. A lost sign-in second factor

Every account holder (Owner, Manager, Admin, Staff, Customer with a portal account) resets it alone, through Anvil's "Allow 2-factor reset by email":

1. On the two-factor screen, the user chooses the reset link.
2. Anvil emails a reset link, valid for 10 minutes.
3. The reset form asks for the user's password.
4. The user enrols a new second factor; the Owner enrols FIDO2 (a security key or the device's passkey).
5. The reset is written to `activity_log`.

## 2. A lost Vault authenticator

The Owner resets it alone:

1. The Owner signs in with password and second factor (section 1 first, if that factor is lost too).
2. In Settings → Vault, the Owner chooses Reset Vault code.
3. The instance emails the Owner a single-use link, valid for 10 minutes.
4. The link clears `vault_totp_secret`. No stored secret is read or changed.
5. On the next open of the Vault, the Owner enrols a new authenticator from a QR code and confirms with one code.
6. The reset is written to `activity_log`, and the Owner is emailed that the Vault code was reset.

## 3. Rules

- The two-factor reset email and the Vault reset link go only to the user's own email address.
- Mybizz never sees, sets, sends or resets a second factor or a Vault code.
- Control of the user's email account and password is what a reset requires; the Owner keeps both secure.

---

*End of file — Vault Recovery Procedure*
