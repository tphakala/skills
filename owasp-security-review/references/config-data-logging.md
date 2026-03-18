# V13–V16: Configuration, Data Protection, Secure Coding, and Logging

OWASP ASVS 5.0 requirements for deployment configuration, secrets management, data protection, secure coding architecture, dependency management, and security logging.

## Table of Contents

- [V13: Configuration](#v13-configuration)
  - [V13.1 Configuration Documentation](#v131-configuration-documentation)
  - [V13.2 Backend Communication Configuration](#v132-backend-communication-configuration)
  - [V13.3 Secret Management](#v133-secret-management)
  - [V13.4 Unintended Information Leakage](#v134-unintended-information-leakage)
- [V14: Data Protection](#v14-data-protection)
  - [V14.1 Data Protection Documentation](#v141-data-protection-documentation)
  - [V14.2 General Data Protection](#v142-general-data-protection)
  - [V14.3 Client-side Data Protection](#v143-client-side-data-protection)
- [V15: Secure Coding and Architecture](#v15-secure-coding-and-architecture)
  - [V15.1 Secure Coding Documentation](#v151-secure-coding-and-architecture-documentation)
  - [V15.2 Security Architecture and Dependencies](#v152-security-architecture-and-dependencies)
  - [V15.3 Defensive Coding](#v153-defensive-coding)
  - [V15.4 Safe Concurrency](#v154-safe-concurrency)
- [V16: Security Logging and Error Handling](#v16-security-logging-and-error-handling)
  - [V16.1 Security Logging Documentation](#v161-security-logging-documentation)
  - [V16.2 General Logging](#v162-general-logging)
  - [V16.3 Security Events](#v163-security-events)

---

## V13: Configuration

### V13.1 Configuration Documentation

| ID | Level | Requirement | CWE |
|----|-------|-------------|-----|
| V13.1.1 | L2 | All communication needs are documented, including external services and cases where end-users might provide an external location for the application to connect to. | — |
| V13.1.2 | L3 | For each service, documentation defines maximum concurrent connections (connection pool limits) and application behavior when the limit is reached, including fallback or recovery mechanisms. | CWE-770 |
| V13.1.3 | L3 | Application documentation defines resource-management strategies for every external system/service: resource-release procedures, timeout settings, failure handling, retry logic with limits, delays, and back-off algorithms. Synchronous HTTP operations mandate short timeouts and disabled or strictly limited retries. | CWE-770 |
| V13.1.4 | L3 | Application documentation defines critical security secrets and rotation schedule based on threat model and business requirements. | — |

### V13.2 Backend Communication Configuration

| ID | Level | Requirement | CWE |
|----|-------|-------------|-----|
| V13.2.1 | L2 | Backend component communications without standard user sessions use individual service accounts, short-term tokens, or certificate-based authentication — not unchanging credentials. | CWE-287 |
| V13.2.2 | L2 | Communications between backend components use accounts assigned least necessary privileges. | CWE-250 |
| V13.2.3 | L2 | If a credential is used for service authentication, it is not a default credential (e.g., root/root, admin/admin). | CWE-521 |
| V13.2.4 | L2 | An allowlist defines external resources/systems the application is permitted to communicate with (outbound requests, data loads, file access). | CWE-918 |
| V13.2.5 | L2 | Web or application server is configured with an allowlist of resources or systems to which it can send requests or load data from. | CWE-918 |
| V13.2.6 | L3 | Where the application connects to separate services, it follows documented configuration for maximum parallel connections, behavior when maximum is reached, connection timeouts, and retry strategies. | — |

### V13.3 Secret Management

| ID | Level | Requirement | CWE |
|----|-------|-------------|-----|
| V13.3.1 | L2 | A secrets management solution (key vault) is used to securely create, store, control access, and destroy backend secrets (passwords, key material, database integrations, third-party keys, token seeds, API keys). Secrets are not in source code or build artifacts. L3 requires a hardware-backed solution (HSM). | CWE-798 |
| V13.3.2 | L2 | Access to secret assets adheres to the principle of least privilege. | CWE-250 |
| V13.3.3 | L3 | All cryptographic operations are performed using an isolated security module (vault or HSM) to protect key material from external exposure. | CWE-321 |
| V13.3.4 | L3 | Secrets are configured to expire and rotate based on application documentation. | CWE-613 |

### V13.4 Unintended Information Leakage

| ID | Level | Requirement | CWE |
|----|-------|-------------|-----|
| V13.4.1 | L1 | Application is deployed without source control metadata (`.git`, `.svn` folders) or these folders are inaccessible externally and to the application. | CWE-200 |
| V13.4.2 | L2 | Debug modes are disabled for all components in production. | CWE-489 |
| V13.4.3 | L2 | Web servers do not expose directory listings unless explicitly intended. | CWE-200 |
| V13.4.4 | L2 | HTTP TRACE method is not supported in production. | CWE-200 |
| V13.4.5 | L2 | Documentation and monitoring endpoints are not exposed unless explicitly intended. | CWE-200 |
| V13.4.6 | L3 | Application does not expose detailed version information of backend components. | CWE-200 |
| V13.4.7 | L3 | Web tier is configured to only serve files with specific extensions to prevent unintentional leakage. | CWE-434 |

---

## V14: Data Protection

### V14.1 Data Protection Documentation

| ID | Level | Requirement | CWE |
|----|-------|-------------|-----|
| V14.1.1 | L2 | All sensitive data is identified and classified by protection level; this includes encoded or obfuscated data. | — |
| V14.1.2 | L2 | Sensitive data protection levels have documented requirements covering encryption, integrity, retention, logging, access, and privacy. | — |

### V14.2 General Data Protection

| ID | Level | Requirement | CWE |
|----|-------|-------------|-----|
| V14.2.1 | L1 | Sensitive data is sent in HTTP body or headers only — never in URL or query string. | CWE-598 |
| V14.2.2 | L2 | Sensitive data is not cached in server components; it is securely purged after use. | CWE-524 |
| V14.2.3 | L2 | Sensitive data is not sent to untrusted parties (analytics trackers, third-party scripts). | CWE-200 |
| V14.2.4 | L2 | Controls around sensitive data are implemented per the documented protection-level requirements. | — |
| V14.2.5 | L3 | Caching mechanisms cache only expected content; no sensitive or dynamic data. Non-existent resources return 404 or 302 rather than cached content. | CWE-524 |
| V14.2.6 | L3 | Only the minimum required sensitive data is returned; data is masked unless the user explicitly requests full view. | CWE-200 |
| V14.2.7 | L3 | Sensitive data subject to retention classification is auto-deleted per schedule. | — |
| V14.2.8 | L3 | User-submitted file metadata sensitive information is removed unless consented to. | CWE-200 |

### V14.3 Client-side Data Protection

| ID | Level | Requirement | CWE |
|----|-------|-------------|-----|
| V14.3.1 | L1 | Authenticated data is cleared from client storage (DOM, localStorage) after session termination. | CWE-200 |
| V14.3.2 | L2 | Sufficient anti-caching headers (`Cache-Control: no-store`) are set for responses containing sensitive data. | CWE-524 |
| V14.3.3 | L2 | Browser storage (localStorage, sessionStorage, IndexedDB, cookies) excludes sensitive data except session tokens. | CWE-922 |

---

## V15: Secure Coding and Architecture

### V15.1 Secure Coding and Architecture Documentation

| ID | Level | Requirement | CWE |
|----|-------|-------------|-----|
| V15.1.1 | L1 | Documentation defines remediation time frames for vulnerable third-party component versions. | — |
| V15.1.2 | L2 | An inventory (SBOM) is maintained; components are sourced from pre-defined trusted repositories. | — |
| V15.1.3 | L2 | Documentation identifies time-consuming or resource-demanding functionality for DoS prevention. | — |
| V15.1.4 | L3 | Documentation highlights "risky components" in third-party libraries. | — |
| V15.1.5 | L3 | Documentation highlights parts using "dangerous functionality." | — |

### V15.2 Security Architecture and Dependencies

| ID | Level | Requirement | CWE |
|----|-------|-------------|-----|
| V15.2.1 | L1 | Application contains only components within documented update/remediation timeframes (no known-vulnerable dependencies). | CWE-1104 |
| V15.2.2 | L2 | Application implements defenses against resource-demanding functionality loss of availability. | CWE-400 |
| V15.2.3 | L2 | Production only includes required functionality; no test, sample, or dev code. | CWE-489 |
| V15.2.4 | L3 | Third-party components and transitive dependencies are sourced from expected repositories; dependency confusion is prevented. | CWE-427 |
| V15.2.5 | L3 | Additional protections (sandboxing, encapsulation, containerization) are applied around dangerous functionality. | — |

### V15.3 Defensive Coding

| ID | Level | Requirement | CWE |
|----|-------|-------------|-----|
| V15.3.1 | L1 | Application returns only the required data-object subset, not entire objects (avoid mass data exposure). | CWE-213 |
| V15.3.2 | L2 | Backend doesn't follow redirects in external URLs unless intended. | CWE-918 |
| V15.3.3 | L2 | Mass assignment protections limit allowed fields per controller/action. | CWE-915 |
| V15.3.4 | L2 | Proxying/middleware transfers the original IP correctly and uses it for logging, rate-limiting, and security decisions. | CWE-348 |
| V15.3.5 | L2 | Variables are type-checked; strict equality/comparators prevent type juggling. | CWE-843 |
| V15.3.6 | L2 | JavaScript prevents prototype pollution (use `Set`/`Map` instead of object literals for dynamic keys). | CWE-1321 |
| V15.3.7 | L2 | HTTP parameter pollution protections are in place; request-source distinction is handled by the framework. | CWE-235 |

### V15.4 Safe Concurrency

| ID | Level | Requirement | CWE |
|----|-------|-------------|-----|
| V15.4.1 | L3 | Shared multi-threaded objects (caches, files, in-memory data) are accessed safely via locks or semaphores. | CWE-362 |
| V15.4.2 | L3 | State checks and dependent actions are performed atomically (preventing TOCTOU race conditions). | CWE-367 |
| V15.4.3 | L3 | Locks are consistent; prevent deadlock and livelock; locking is managed by responsible code. | CWE-833 |
| V15.4.4 | L3 | Resource allocation policies prevent thread starvation via fair resource access. | CWE-410 |

---

## V16: Security Logging and Error Handling

### V16.1 Security Logging Documentation

| ID | Level | Requirement | CWE |
|----|-------|-------------|-----|
| V16.1.1 | L2 | An inventory documents logging per stack layer: what is logged, format, storage, access, and retention. | — |

### V16.2 General Logging

| ID | Level | Requirement | CWE |
|----|-------|-------------|-----|
| V16.2.1 | L2 | Log entries include metadata (when, where, who, what) for detailed investigation. | CWE-778 |
| V16.2.2 | L2 | Log time sources are synchronized; timestamps use UTC or include time zone offset. | CWE-778 |
| V16.2.3 | L2 | Application stores or broadcasts logs only to documented services. | CWE-532 |
| V16.2.4 | L2 | Logs are readable and correlatable by log processors; preferably in a common format. | CWE-778 |
| V16.2.5 | L2 | Sensitive data logging is enforced per protection level: no credentials, no payment data; session tokens are hashed. | CWE-532 |

### V16.3 Security Events

| ID | Level | Requirement | CWE |
|----|-------|-------------|-----|
| V16.3.1 | L2 | All authentication operations are logged (successful and unsuccessful); authentication type and factors are collected. | CWE-778 |
| V16.3.2 | L2 | Failed authorization attempts are logged. L3 logs all authorization decisions and sensitive data access. | CWE-778 |
| V16.3.3 | L2 | Security events are logged: control bypass attempts (validation, logic, anti-automation). | CWE-778 |
| V16.3.4 | L2 | Session invalidation on explicit logout or expiration is logged; no further session use is allowed. | CWE-778 |
