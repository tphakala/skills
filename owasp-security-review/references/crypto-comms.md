# V11: Cryptography + V12: Secure Communication

OWASP ASVS 5.0 requirements for cryptographic implementations, key management, hashing, random values, and transport-layer security.

## Table of Contents

- [V11: Cryptography](#v11-cryptography)
  - [V11.1 Cryptographic Inventory and Documentation](#v111-cryptographic-inventory-and-documentation)
  - [V11.2 Secure Cryptography Implementation](#v112-secure-cryptography-implementation)
  - [V11.3 Encryption Algorithms](#v113-encryption-algorithms)
  - [V11.4 Hashing and Hash-based Functions](#v114-hashing-and-hash-based-functions)
  - [V11.5 Random Values](#v115-random-values)
  - [V11.6 Public Key Cryptography](#v116-public-key-cryptography)
  - [V11.7 In-Use Data Cryptography](#v117-in-use-data-cryptography)
- [V12: Secure Communication](#v12-secure-communication)
  - [V12.1 General TLS Security Guidance](#v121-general-tls-security-guidance)
  - [V12.2 HTTPS Communication with External Facing Services](#v122-https-communication-with-external-facing-services)
  - [V12.3 General Service to Service Communication Security](#v123-general-service-to-service-communication-security)

---

## V11: Cryptography

### V11.1 Cryptographic Inventory and Documentation

| ID | Level | Requirement | CWE |
|----|-------|-------------|-----|
| V11.1.1 | L2 | Key management policy and lifecycle are documented following NIST SP 800-57. | CWE-320 |
| V11.1.2 | L2 | A cryptographic inventory is maintained documenting all keys, algorithms, certificates, and their usage. | CWE-320 |
| V11.1.3 | L3 | Cryptographic discovery mechanisms identify all encryption, hashing, and signing operations. | — |
| V11.1.4 | L3 | The inventory is maintained with a migration plan for post-quantum cryptography. | — |

### V11.2 Secure Cryptography Implementation

| ID | Level | Requirement | CWE |
|----|-------|-------------|-----|
| V11.2.1 | L2 | Industry-validated cryptographic implementations are used (well-known libraries or hardware modules), not custom implementations. | CWE-327 |
| V11.2.2 | L2 | Crypto agility: algorithms, keys, key lengths, and rounds are reconfigurable without code changes. | CWE-327 |
| V11.2.3 | L2 | All cryptographic primitives provide ≥128-bit security (256-bit ECC, 3072-bit RSA minimum). | CWE-326 |
| V11.2.4 | L3 | All cryptographic operations are constant-time; no short-circuit comparisons are used. | CWE-208 |
| V11.2.5 | L3 | Cryptographic modules fail securely; errors do not enable vulnerabilities. | CWE-310 |

### V11.3 Encryption Algorithms

| ID | Level | Requirement | CWE |
|----|-------|-------------|-----|
| V11.3.1 | L1 | Insecure block modes (ECB) and weak padding (PKCS#1 v1.5) are not used. | CWE-327 |
| V11.3.2 | L1 | Only approved ciphers and modes are used (e.g., AES-GCM, ChaCha20-Poly1305). | CWE-327 |
| V11.3.3 | L2 | Encrypted data is protected via authenticated encryption (e.g., AES-GCM) or encryption followed by MAC. | CWE-327 |
| V11.3.4 | L3 | Nonces and IVs are not reused for the same key/data-element pair. | CWE-327 |
| V11.3.5 | L3 | Encryption and MAC combination operates in encrypt-then-MAC mode. | CWE-327 |

### V11.4 Hashing and Hash-based Functions

| ID | Level | Requirement | CWE |
|----|-------|-------------|-----|
| V11.4.1 | L1 | Only approved hash functions are used; MD5 is not used for any cryptographic purpose. | CWE-328 |
| V11.4.2 | L2 | Passwords are stored via an approved key derivation function (bcrypt, scrypt, Argon2, PBKDF2). | CWE-916 |
| V11.4.3 | L2 | Hash functions used for digital signatures are collision-resistant with ≥256-bit output. | CWE-328 |
| V11.4.4 | L2 | Key derivation for password secrets uses approved functions with sufficient stretching iterations. | CWE-916 |

### V11.5 Random Values

| ID | Level | Requirement | CWE |
|----|-------|-------------|-----|
| V11.5.1 | L2 | Non-guessable random numbers and strings are generated via a CSPRNG with ≥128 bits of entropy. | CWE-330 |
| V11.5.2 | L3 | The RNG mechanism is securely designed for heavy-demand scenarios (high concurrency, key generation). | CWE-330 |

### V11.6 Public Key Cryptography

| ID | Level | Requirement | CWE |
|----|-------|-------------|-----|
| V11.6.1 | L2 | Approved algorithms and modes are used for key generation; no insecure key generation methods. | CWE-326 |
| V11.6.2 | L3 | Approved algorithms are used for key exchange (e.g., Diffie-Hellman) with secure parameters. | CWE-326 |

### V11.7 In-Use Data Cryptography

| ID | Level | Requirement | CWE |
|----|-------|-------------|-----|
| V11.7.1 | L3 | Full memory encryption protects sensitive data during use. | — |
| V11.7.2 | L3 | Data minimization: sensitive data is encrypted immediately after use or as soon as possible. | — |

---

## V12: Secure Communication

### V12.1 General TLS Security Guidance

| ID | Level | Requirement | CWE |
|----|-------|-------------|-----|
| V12.1.1 | L1 | Only latest recommended TLS protocol versions are enabled (TLS 1.2 and TLS 1.3); the latest version is the preferred option. | CWE-327 |
| V12.1.2 | L2 | Only recommended cipher suites are enabled, with the strongest set as preferred. L3 applications only support cipher suites providing forward secrecy. | CWE-327 |
| V12.1.3 | L2 | Application validates mTLS client certificates are trusted before using certificate identity for authentication or authorization. | CWE-295 |
| V12.1.4 | L3 | Proper certificate revocation is configured, such as OCSP Stapling. | CWE-295 |
| V12.1.5 | L3 | Encrypted Client Hello (ECH) is enabled in TLS settings to prevent exposure of SNI during TLS handshake. | CWE-319 |

### V12.2 HTTPS Communication with External Facing Services

| ID | Level | Requirement | CWE |
|----|-------|-------------|-----|
| V12.2.1 | L1 | TLS is used for all connectivity between client and external-facing HTTP-based services; no fallback to insecure or unencrypted communication. | CWE-319 |
| V12.2.2 | L1 | External-facing services use publicly trusted TLS certificates. | CWE-295 |

### V12.3 General Service to Service Communication Security

| ID | Level | Requirement | CWE |
|----|-------|-------------|-----|
| V12.3.1 | L2 | Encrypted protocols (TLS) are used for all inbound and outbound connections including monitoring, management tools, remote access, SSH, middleware, databases, and APIs. No fallback to insecure protocols. | CWE-319 |
| V12.3.2 | L2 | TLS clients validate certificates received before communicating. | CWE-295 |
| V12.3.3 | L2 | TLS or appropriate transport encryption is used for all internal HTTP-based service connectivity. | CWE-319 |
| V12.3.4 | L2 | Internal TLS connections use trusted certificates; internally generated or self-signed certificates require consuming services to be configured to trust specific internal CAs. | CWE-295 |
| V12.3.5 | L3 | Internal services use strong mutual authentication (TLS client authentication); a service mesh is recommended for microservice architectures. | CWE-295 |
