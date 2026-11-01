---
title: "development-policy"
doc-id: "mb-509458a0"
updated: "2026-11-01"
---
# Mybizz CS — Development Policy

**Category:** Policy
**Authority:** Mandatory — all development work must conform to this policy

**Scope:** How `mb-3-cs` development is run. Technical standards are in [[[default:specifications-global/spec-anvil-platform-standards]]](C:/dev/project-library-global/specifications-global/spec-anvil-platform-standards.md), [[[default:specifications-global/spec-m3-design-standards]]](C:/dev/project-library-global/specifications-global/spec-m3-design-standards.md) and [[[default:specifications-global/spec-security-architecture]]](C:/dev/project-library-global/specifications-global/spec-security-architecture.md).

---

## 1. The Anvil-First Mandate

Mybizz is an Anvil application. Every technical decision defaults to the Anvil way. Where Anvil
provides a facility — authentication, data persistence, background tasks, email, secrets — that
facility is used. Custom alternatives are not introduced unless Anvil's facility is genuinely
inadequate.

**Before implementing any feature, check in this order:**
1. Anvil's built-in components and services
2. Anvil documentation for native solutions
3. Anvil's example apps for established patterns
4. Anvil community forum for community approaches
5. Only then consider a custom implementation — and document the justification

**Approved exceptions:**

| Exception | Used instead of | Reason |
|---|---|---|
| The Vault | Anvil Secrets, for client credentials | Client API keys cannot go in Anvil Secrets, because that would require giving clients editor access. The Vault is an application-level encrypted secrets store ([[[default:specifications-global/spec-vault-system]]](C:/dev/project-library-global/specifications-global/spec-vault-system.md)). Anvil Secrets holds only `encryption_key` |
| Amazon SES, called through `server_shared/email_service` and set as Anvil's Custom Mail Server | Anvil's built-in email, for every email received and sent | Anvil publishes no self-service way to sign a client's own domain with DKIM, and its email has no custom headers (one-click unsubscribe), no bounce or complaint reports and no spam scanning of received email. SES receives every email to the client's domain, signs every email sent from it, keeps each client's sending reputation separate, and reports bounces, complaints, opens and clicks (F-18, `team-inbox-spec.md`) |
| Cloudflare R2 | Data Tables, for backups, history, webhook payloads, audit entries older than 30 days, email bodies older than 90 days, attachments and receipts | The client's cold data is kept in the client's own Cloudflare account, outside the instance, and the instance's tables stay small (F-29) |
| Groq, called through App 5 | — (Anvil has no AI or speech service) | AI text, speech-to-text and natural-sounding speech (F-26, F-27); App 5 holds Mybizz's keys and records each client's usage, so no client instance holds an AI key |

A new exception is added to this table, with its reason, before it is built.

---

## 2. Version Control and Backup

All development happens in App 1 (`mb-3-cs-app1`). Agents never commit or push; the developer commits every repository with `commit-all`. The only other commits are the automatic commits of Anvil's AI agent in the Anvil editor (section 3). A release is App 1's code tagged `vMAJOR.MINOR.PATCH` and pushed by the developer into App 2 (Step 42), as [[[default:specifications-global/spec-deployment]]](C:/dev/project-library-global/specifications-global/spec-deployment.md) sets out.

Backup protocol: see [[[default:specifications-global/spec-testing-methodology-standards]]](C:/dev/project-library-global/specifications-global/spec-testing-methodology-standards.md) section 4 — App 1 is backed up before and after every test run on hosted App 1; every client instance is backed up to its own R2 bucket before a release. Never back up a red or failing state.

---

## 3. Development Tooling

- **OpenCode**, used through its web UI, with the PDLF step skills, working in WSL.
- **Anvil's AI agent**, in the Anvil editor, used for two jobs: building each form from its approved wireframe, screen HTML and screen PNG (Step 29), and preparing schema changes from `schema-migration-log.md` (Step 17). The agent cannot apply a schema change; the developer applies every schema change in the Data Tables view through **Resolve**. What the agent can and cannot do, and how it is prompted: [[[default:anvil-docs/ai-agent/anvil-ai-agent|The Anvil AI Agent]]](C:/dev/project-library-global/anvil-docs/ai-agent/anvil-ai-agent.md).
- **GitHub** is the shared source of truth for every Anvil app. Changes made in the Anvil Designer or by Anvil's AI agent are pulled into the local copy with Step 18's pull procedure before any agent edits code.

---

## 4. Enforcement

Every finding is fixed within the step that found it. The level sets the order of the fixes, Critical first, and how the step responds:

| Level | Definition | Response |
|---|---|---|
| Critical | Security issue or data loss risk | Stop all other work; fix first |
| High | Policy violation | Fix before any Medium or Low finding |
| Medium | Best practice deviation | Fix within the step |
| Low | Style or formatting | Fix within the step |

A finding that cannot be fixed within the step, because its fix depends on something outside the project, is recorded in `pdlf-todo.md` with what it depends on.

Security issues are always Critical. RBAC violations, secret handling errors, and any code that
returns sensitive data to the client are treated as Critical.
