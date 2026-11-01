---
title: "crm-package-replaces-marketing"
doc-id: "mb-6bc802c4"
updated: "2026-11-01"
---
# Package Name: crm/ for CRM and Marketing Forms

**Status:** Accepted
**Date:** 2026-10-31

---

## Context

In Mybizz CS, CRM and marketing are one concern: contacts, segments, tasks, automations, campaigns, broadcasts and lead capture all work on the same contact records (F-17 to F-20). The client-side package that holds these forms needs one name that every document, prompt and agent uses.

---

## Decision

**The client-side package for the CRM and marketing forms is `client_code/crm/`.** No client package is named `marketing/`.

- The admin menu group is labelled **Customers & Marketing**. The label is what users see; the package name is what code uses. They differ by design.
- The server-side package is `server_marketing/`. The two names are not interchangeable: `crm/` is client code, `server_marketing/` is server code.
- The forms in `crm/` are listed in `scaffold-spec.md` (Package Structure).

---

## Consequences

- Every document, prompt and rules file names the client package `crm/` and the server package `server_marketing/`.
- A reference to a client package `marketing/` is an error and is corrected to `crm/`.

---

*End of `crm-package-replaces-marketing` ADR*
