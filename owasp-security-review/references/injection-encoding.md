# V1: Encoding and Sanitization + V2: Validation and Business Logic

OWASP ASVS 5.0 requirements for input handling, output encoding, injection prevention, and business logic security.

## Table of Contents

- [V1.1 Encoding and Sanitization Architecture](#v11-encoding-and-sanitization-architecture)
- [V1.2 Injection Prevention](#v12-injection-prevention)
- [V1.3 Sanitization](#v13-sanitization)
- [V1.4 Memory, String, and Unmanaged Code](#v14-memory-string-and-unmanaged-code)
- [V1.5 Safe Deserialization](#v15-safe-deserialization)
- [V2.1 Validation and Business Logic Documentation](#v21-validation-and-business-logic-documentation)
- [V2.2 Input Validation](#v22-input-validation)
- [V2.3 Business Logic Security](#v23-business-logic-security)
- [V2.4 Anti-automation](#v24-anti-automation)

---

## V1.1 Encoding and Sanitization Architecture

| ID | Level | Requirement | CWE |
|----|-------|-------------|-----|
| V1.1.1 | L2 | Input is decoded or unescaped into a canonical form only once, and this happens before any further processing such as validation, sanitization, or encoding for output. | — |
| V1.1.2 | L2 | Output encoding or escaping is performed as the final step, immediately before the content is passed to the target interpreter. | — |

## V1.2 Injection Prevention

| ID | Level | Requirement | CWE |
|----|-------|-------------|-----|
| V1.2.1 | L1 | Output encoding is context-appropriate for the interpreter being used (HTML elements, HTML attributes, CSS, JavaScript, URI components, HTTP headers, etc.). | CWE-79 |
| V1.2.2 | L1 | When building dynamic URLs with untrusted data, the data is encoded according to the URL context and only safe URL protocols (e.g., not `javascript:` or `data:`) are permitted. | CWE-79 |
| V1.2.3 | L1 | Dynamic JavaScript uses output encoding or escaping to prevent script injection. | CWE-79 |
| V1.2.4 | L1 | Database queries use parameterized queries, ORMs, or other mechanisms that prevent SQL injection. | CWE-89 |
| V1.2.5 | L1 | OS command injection is prevented using parameterized OS queries or equivalent controls. | CWE-78 |
| V1.2.6 | L2 | LDAP injection is prevented by applying LDAP-specific security controls. | CWE-90 |
| V1.2.7 | L2 | XPath injection is prevented via query parameterization or compiled queries. | CWE-643 |
| V1.2.8 | L2 | LaTeX processors are secured with an allowlist of commands; injection of LaTeX commands is prevented. | — |
| V1.2.9 | L2 | Regular expression metacharacters from untrusted input are escaped to prevent metacharacter misinterpretation. | CWE-1333 |
| V1.2.10 | L3 | CSV/Formula injection is prevented; special characters are escaped per RFC 4180. | CWE-1236 |

## V1.3 Sanitization

| ID | Level | Requirement | CWE |
|----|-------|-------------|-----|
| V1.3.1 | L1 | WYSIWYG editor input is sanitized using a secure HTML sanitization library. | CWE-79 |
| V1.3.2 | L1 | Dynamic code execution functions are avoided; where no alternative exists, user input is sanitized before use. This includes language-specific code execution functions such as JavaScript's Function constructor, Go's plugin system, and similar mechanisms in other languages. | CWE-95 |
| V1.3.3 | L2 | Data used in dangerous contexts is sanitized to allow only safe characters; length is enforced. | — |
| V1.3.4 | L2 | SVG scriptable content is validated and sanitized; no inline scripts or `foreignObject` elements allowed. | CWE-79 |
| V1.3.5 | L2 | Scriptable or expression-capable content (Markdown, CSS stylesheets, XSL, BBCode) is sanitized. | CWE-79 |
| V1.3.6 | L2 | SSRF is mitigated using allowlist validation and by sanitizing dangerous characters in URLs. | CWE-918 |
| V1.3.7 | L2 | Template injection is prevented; untrusted input is not used in template creation. | CWE-94 |
| V1.3.8 | L2 | JNDI queries are configured securely and untrusted input is sanitized before use (preventing Log4Shell-class attacks). | CWE-917 |
| V1.3.9 | L2 | Memcache content is sanitized to prevent injection. | — |
| V1.3.10 | L2 | Format strings from untrusted input are sanitized to prevent malicious resolution. | CWE-134 |
| V1.3.11 | L2 | User input is sanitized before use in mail systems (SMTP/IMAP) to prevent header injection. | CWE-93 |
| V1.3.12 | L3 | Regular expressions are free from exponential backtracking (ReDoS); untrusted input used in regex is sanitized. | CWE-1333 |

## V1.4 Memory, String, and Unmanaged Code

| ID | Level | Requirement | CWE |
|----|-------|-------------|-----|
| V1.4.1 | L2 | Memory-safe strings and pointers are used to prevent buffer overflow. | CWE-120 |
| V1.4.2 | L2 | Sign, range, and input validation prevents integer overflow. | CWE-190 |
| V1.4.3 | L2 | Allocated memory is properly released; freed pointers are set to null. | CWE-401 |

**Note:** V1.4 primarily applies to languages with manual memory management (C, C++, Rust unsafe blocks, CGo). For memory-safe languages, these are generally satisfied by the language runtime.

## V1.5 Safe Deserialization

| ID | Level | Requirement | CWE |
|----|-------|-------------|-----|
| V1.5.1 | L1 | XML parsers are configured restrictively; unsafe features (external entities, DTD processing) are disabled to prevent XXE. | CWE-611 |
| V1.5.2 | L2 | Untrusted deserialization enforces safe input using allowlists or type restrictions. | CWE-502 |
| V1.5.3 | L3 | Consistent parsing is ensured across different parsers; the same character encoding is used. | — |

---

## V2.1 Validation and Business Logic Documentation

| ID | Level | Requirement | CWE |
|----|-------|-------------|-----|
| V2.1.1 | L1 | Documentation defines input validation rules for all expected data structures. | — |
| V2.1.2 | L2 | Documentation defines logical/contextual validation rules (e.g., suburb must match ZIP code). | — |
| V2.1.3 | L2 | Documentation defines business logic limits and validations (per-user and global). | — |

## V2.2 Input Validation

| ID | Level | Requirement | CWE |
|----|-------|-------------|-----|
| V2.2.1 | L1 | Input is validated against an allowlist, expected patterns, ranges, or predefined rules. | CWE-20 |
| V2.2.2 | L1 | Input validation is enforced at a trusted service layer, not solely on the client side. | CWE-602 |
| V2.2.3 | L2 | Related data combinations are validated as reasonable per predefined rules. | CWE-20 |

## V2.3 Business Logic Security

| ID | Level | Requirement | CWE |
|----|-------|-------------|-----|
| V2.3.1 | L1 | Business logic flows execute in expected sequential order; steps cannot be skipped. | CWE-841 |
| V2.3.2 | L2 | Business logic limits are implemented per documentation. | — |
| V2.3.3 | L2 | Transactions are used at the business logic level; operations succeed or roll back entirely. | — |
| V2.3.4 | L2 | Business logic locking prevents double-booking of limited resources. | CWE-367 |
| V2.3.5 | L3 | High-value flows require multi-user approval. | — |

## V2.4 Anti-automation

| ID | Level | Requirement | CWE |
|----|-------|-------------|-----|
| V2.4.1 | L2 | Anti-automation controls prevent excessive function calls, DoS, and quota exhaustion. | CWE-770 |
| V2.4.2 | L3 | Business logic flows require realistic human timing to prevent automated abuse. | — |
