---
name: owasp-security-review
description: Perform a systematic security review of code based on the OWASP Application Security Verification Standard (ASVS) 5.0. Use this skill whenever the user asks for a security review, security audit, OWASP review, ASVS compliance check, threat analysis of code, or mentions checking code for security vulnerabilities in a structured or comprehensive way. Also use when another skill or workflow delegates a deeper security analysis, or when the user asks about secure coding practices for authentication, authorization, cryptography, session management, input validation, or data protection. This is distinct from general code review — it specifically maps findings to OWASP ASVS requirement IDs and produces a compliance-oriented security report.
license: Apache-2.0
metadata:
  author: Tomi P. Hakala
  version: "1.0.0"
  asvs-version: "5.0.0"
  source: https://github.com/OWASP/ASVS
---

# OWASP Security Review

Perform a structured security review of code using the OWASP Application Security Verification Standard (ASVS) 5.0 as the framework. Every finding maps to a specific ASVS requirement ID so the report is actionable and auditable.

## Overview

OWASP ASVS 5.0 defines 250+ security requirements across 16 chapters, organized into three verification levels:

- **L1 (Baseline)**: Minimum security controls every application needs. Low-hanging fruit and common vulnerability classes.
- **L2 (Standard)**: Defense-in-depth for most applications handling sensitive data. This is the default review level.
- **L3 (Advanced)**: High-assurance systems — financial, medical, critical infrastructure. Includes protection against sophisticated attacks.

Each level is cumulative: L2 includes all L1 requirements, L3 includes all L2 requirements.

## When This Skill Runs

This skill produces a security-focused report organized by ASVS domain. It does not replace general code review (logic bugs, code smells, performance) — it complements it by providing structured coverage of security concerns.

## Review Process

### Step 1: Scope the Review

Determine what to review and at what depth.

**Verification level**: Default to **L2** unless the user specifies otherwise. If the user says "basic" or "quick", use L1. If they mention "high assurance", "compliance", "financial", or "medical", use L3.

**Identify relevant ASVS domains** by reading the code and noting what it does:

| Code handles...                                | Read reference file           | ASVS Chapters |
|-------------------------------------------------|-------------------------------|----------------|
| User input, form processing, queries, templates | `injection-encoding.md`       | V1, V2         |
| Browser-rendered HTML, JS, CSS, cookies, CORS   | `web-frontend.md`             | V3             |
| REST/GraphQL/WebSocket APIs, file upload/download| `api-file-handling.md`        | V4, V5         |
| Login, passwords, MFA, OAuth, JWT, sessions     | `auth-session-tokens.md`      | V6–V10         |
| Encryption, hashing, TLS, key management        | `crypto-comms.md`             | V11, V12       |
| Config, secrets, env vars, logging, error handling, dependencies | `config-data-logging.md` | V13–V16 |

Only load reference files for domains the code actually touches. Most reviews need 2–4 reference files. If the code touches only authentication, don't load the crypto or frontend references.

### Step 2: Read the Code

Read all files under review thoroughly before checking any requirements. Understand:

- What the code does and its trust boundaries
- Where user input enters and how it flows through the system
- What external systems it communicates with
- What data it stores and how sensitive that data is
- What authentication and authorization mechanisms are in place

### Step 3: Check Requirements Systematically

For each relevant ASVS domain, read the corresponding reference file and check every requirement at or below the selected verification level against the code.

For each requirement, determine one of:
- **Violation found** — Code actively violates this requirement
- **Not implemented** — The requirement applies but no implementation exists (e.g., no rate limiting on auth endpoints)
- **Satisfied** — Code meets the requirement
- **Not applicable** — Requirement doesn't apply to this code (e.g., LDAP injection checks when no LDAP is used)

Focus your report on violations and missing implementations. Don't list every satisfied or N/A requirement — that's noise.

### Step 4: Classify Severity

Map each finding to a severity level based on exploitability and impact:

| Severity     | Criteria                                                                                     |
|--------------|----------------------------------------------------------------------------------------------|
| **Critical** | Directly exploitable, leads to data breach / system compromise / auth bypass. Fix immediately.|
| **High**     | Exploitable with some conditions, significant security impact. Fix before release.           |
| **Medium**   | Requires specific conditions to exploit, moderate impact. Plan remediation.                  |
| **Low**      | Defense-in-depth gap, minimal direct impact. Track and address.                              |
| **Info**     | Best-practice recommendation, no direct vulnerability. Consider adopting.                    |

### Step 5: Produce the Report

Use this exact format:

```markdown
## OWASP Security Review: [scope description]

**Review level**: L1 / L2 / L3
**ASVS domains checked**: [list which chapters were reviewed]
**Files reviewed**: [list of files]

---

### Critical Findings

#### [Finding title]
- **ASVS Ref**: V[X].[Y].[Z] — [requirement description]
- **Location**: `file:line`
- **Issue**: [What the code does wrong or fails to do]
- **Impact**: [What an attacker could achieve]
- **Remediation**: [Specific fix with code example]

---

### High Findings
[Same structure]

### Medium Findings
[Same structure]

### Low Findings
[Same structure]

### Informational
[Same structure]

---

### Summary

| Severity | Count |
|----------|-------|
| Critical | X     |
| High     | X     |
| Medium   | X     |
| Low      | X     |
| Info     | X     |

### ASVS Coverage

| Domain                        | Requirements Checked | Violations | Not Implemented | Satisfied |
|-------------------------------|----------------------|-----------|-----------------|-----------|
| V1: Encoding & Sanitization   | X                    | X         | X               | X         |
| ...                           |                      |           |                 |           |

### Recommendations

[Prioritized list of next steps, grouped by effort level]
```

## Important Guidelines

**Be specific, not generic.** Every finding must reference a concrete line of code and a specific ASVS requirement. "Input validation could be improved" is not a finding. "User-supplied `query` parameter at `handler.go:45` is concatenated directly into SQL query without parameterization, violating V1.2.4" is a finding.

**Show the fix.** Every violation or missing implementation should include a remediation code example in the language of the code being reviewed.

**Don't fabricate findings.** If the code looks secure for a given domain, say so. A clean report is a valid outcome. Never invent findings to make the report look thorough.

**Respect scope.** Only check requirements relevant to the code under review. A utility function that parses dates doesn't need OAuth checks.

**Note assumptions.** If you can't determine whether a requirement is met without seeing other parts of the system (e.g., "is TLS configured on the load balancer?"), note it as "Unable to verify — requires infrastructure review" rather than marking it as a violation.

## Reference Files

The `references/` directory contains the full ASVS 5.0 requirements organized by domain. Each file lists every requirement with its ID, verification level, description, and CWE mapping where available. Only read the files you need for the code under review.

- `references/injection-encoding.md` — V1 (Encoding & Sanitization) + V2 (Validation & Business Logic)
- `references/web-frontend.md` — V3 (Web Frontend Security)
- `references/api-file-handling.md` — V4 (API & Web Service) + V5 (File Handling)
- `references/auth-session-tokens.md` — V6 (Authentication) + V7 (Session Management) + V8 (Authorization) + V9 (Self-contained Tokens) + V10 (OAuth & OIDC)
- `references/crypto-comms.md` — V11 (Cryptography) + V12 (Secure Communication)
- `references/config-data-logging.md` — V13 (Configuration) + V14 (Data Protection) + V15 (Secure Coding & Architecture) + V16 (Security Logging & Error Handling)
