# V6–V10: Authentication, Session Management, Authorization, Tokens, and OAuth/OIDC

OWASP ASVS 5.0 requirements for identity, access control, and token security.

## Table of Contents

- [V6: Authentication](#v6-authentication)
  - [V6.1 Documentation](#v61-authentication-documentation)
  - [V6.2 Password Security](#v62-password-security)
  - [V6.3 General Authentication Security](#v63-general-authentication-security)
  - [V6.4 Authentication Factor Lifecycle and Recovery](#v64-authentication-factor-lifecycle-and-recovery)
  - [V6.5 General Multi-factor Authentication](#v65-general-multi-factor-authentication-requirements)
  - [V6.6 Out-of-Band Authentication](#v66-out-of-band-authentication-mechanisms)
  - [V6.7 Cryptographic Authentication](#v67-cryptographic-authentication-mechanism)
  - [V6.8 Authentication with an Identity Provider](#v68-authentication-with-an-identity-provider)
- [V7: Session Management](#v7-session-management)
  - [V7.1 Documentation](#v71-session-management-documentation)
  - [V7.2 Fundamental Session Management Security](#v72-fundamental-session-management-security)
  - [V7.3 Session Timeout](#v73-session-timeout)
  - [V7.4 Session Termination](#v74-session-termination)
  - [V7.5 Defenses Against Session Abuse](#v75-defenses-against-session-abuse)
  - [V7.6 Federated Re-authentication](#v76-federated-re-authentication)
- [V8: Authorization](#v8-authorization)
  - [V8.1 Documentation](#v81-authorization-documentation)
  - [V8.2 General Authorization Design](#v82-general-authorization-design)
  - [V8.3 Operation Level Authorization](#v83-operation-level-authorization)
  - [V8.4 Other Authorization Considerations](#v84-other-authorization-considerations)
- [V9: Self-contained Tokens](#v9-self-contained-tokens)
  - [V9.1 Token Source and Integrity](#v91-token-source-and-integrity)
  - [V9.2 Token Content](#v92-token-content)
- [V10: OAuth and OIDC](#v10-oauth-and-oidc)
  - [V10.1 Generic OAuth and OIDC Security](#v101-generic-oauth-and-oidc-security)
  - [V10.2 OAuth Client](#v102-oauth-client)
  - [V10.3 OAuth Resource Server](#v103-oauth-resource-server)
  - [V10.4 OAuth Authorization Server](#v104-oauth-authorization-server)
  - [V10.5 OIDC Client](#v105-oidc-client)
  - [V10.6 OpenID Provider](#v106-openid-provider)
  - [V10.7 Consent Management](#v107-consent-management)

---

## V6: Authentication

### V6.1 Authentication Documentation

| ID | Level | Requirement | CWE |
|----|-------|-------------|-----|
| V6.1.1 | L1 | Documentation defines rate limiting, anti-automation, and adaptive response controls for authentication. | — |
| V6.1.2 | L2 | A context-specific word list is documented to prevent password reuse with weak variations. | — |
| V6.1.3 | L2 | Multiple authentication pathways are documented with consistent security enforcement. | — |

### V6.2 Password Security

| ID | Level | Requirement | CWE |
|----|-------|-------------|-----|
| V6.2.1 | L1 | User passwords are minimum 8 characters (15+ recommended). | CWE-521 |
| V6.2.2 | L1 | Users can change their password. | — |
| V6.2.3 | L1 | Password change requires the current password and a new password. | CWE-620 |
| V6.2.4 | L1 | Registration and password change are checked against the top 3,000 most common passwords. | CWE-521 |
| V6.2.5 | L1 | Passwords of any composition are allowed; no character-type restrictions are imposed. | CWE-521 |
| V6.2.6 | L1 | Password fields use `type=password`; may allow temporary visibility toggle. | — |
| V6.2.7 | L1 | Paste functionality and password managers are permitted in password fields. | CWE-521 |
| V6.2.8 | L1 | Passwords are verified exactly as received — no truncation or case normalization. | CWE-521 |
| V6.2.9 | L2 | Passwords of 64+ characters are permitted. | CWE-521 |
| V6.2.10 | L2 | Passwords remain valid until compromised or rotated by the user; no periodic forced rotation. | CWE-262 |
| V6.2.11 | L2 | A context-specific word list is used to prevent weak password creation. | CWE-521 |
| V6.2.12 | L2 | Registration and password change are checked against a breached password database. | CWE-521 |

### V6.3 General Authentication Security

| ID | Level | Requirement | CWE |
|----|-------|-------------|-----|
| V6.3.1 | L1 | Credential stuffing and brute force controls are implemented per documentation. | CWE-307 |
| V6.3.2 | L1 | Default accounts (root, admin, sa) are absent or disabled. | CWE-798 |
| V6.3.3 | L2 | Multi-factor authentication or a combination of single-factor mechanisms is required. L3 requires hardware-based factors. | CWE-308 |
| V6.3.4 | L2 | Multiple authentication pathways are documented; no undocumented pathways exist. | — |
| V6.3.5 | L3 | Users are notified of suspicious authentication attempts (new location, unusual client, inactivity). | — |
| V6.3.6 | L3 | Email is not used as a single or multi-factor authentication mechanism. | CWE-304 |
| V6.3.7 | L3 | Users are notified after authentication detail updates (resets, credential changes). | — |
| V6.3.8 | L3 | Valid users are not deducible from failed challenges (via error messages or timing differences). | CWE-203 |

### V6.4 Authentication Factor Lifecycle and Recovery

| ID | Level | Requirement | CWE |
|----|-------|-------------|-----|
| V6.4.1 | L1 | System-generated passwords and codes are random, expire quickly, and are single-use. | CWE-330 |
| V6.4.2 | L1 | No password hints or knowledge-based authentication. | CWE-640 |
| V6.4.3 | L2 | Secure password reset does not bypass enabled MFA. | CWE-308 |
| V6.4.4 | L2 | Lost MFA recovery requires identity proofing at enrollment-equivalent level. | — |
| V6.4.5 | L3 | Expiring authentication mechanisms: renewal instructions are sent in advance. | — |
| V6.4.6 | L3 | Admins can initiate password reset but cannot change or choose the user's password. | — |

### V6.5 General Multi-factor Authentication Requirements

| ID | Level | Requirement | CWE |
|----|-------|-------------|-----|
| V6.5.1 | L2 | Lookup secrets, OOB codes, and TOTPs are usable only once. | CWE-308 |
| V6.5.2 | L2 | Lookup secrets with <112 bits entropy are hashed with a 32-bit salt; ≥112 bits can be stored as-is. | CWE-328 |
| V6.5.3 | L2 | Lookup secrets, OOB codes, and TOTP seeds are generated via a CSPRNG. | CWE-330 |
| V6.5.4 | L2 | Lookup and OOB codes have minimum 20 bits of entropy (4 alphanumeric or 6 digits). | CWE-330 |
| V6.5.5 | L2 | OOB requests have maximum 10-minute lifetime; TOTPs have maximum 30-second lifetime. | CWE-613 |
| V6.5.6 | L3 | Any authentication factor is revocable if stolen or lost. | — |
| V6.5.7 | L3 | Biometric authentication is only used as a secondary factor with something-you-have or something-you-know. | — |
| V6.5.8 | L3 | TOTPs are verified against a trusted time source, not client-provided time. | CWE-367 |

### V6.6 Out-of-Band Authentication Mechanisms

| ID | Level | Requirement | CWE |
|----|-------|-------------|-----|
| V6.6.1 | L2 | PSTN OTP is offered only after phone validation; stronger methods are also offered. L3 forbids PSTN. | CWE-304 |
| V6.6.2 | L2 | OOB codes/tokens are bound to the original request; not reusable across requests. | CWE-287 |
| V6.6.3 | L2 | OOB code is protected via rate limiting; ≥64 bits entropy recommended. | CWE-307 |
| V6.6.4 | L3 | Push notifications are protected against push bombing via rate limiting. | CWE-307 |

### V6.7 Cryptographic Authentication Mechanism

| ID | Level | Requirement | CWE |
|----|-------|-------------|-----|
| V6.7.1 | L3 | Cryptographic authentication certificates are stored to prevent modification. | CWE-321 |
| V6.7.2 | L3 | Challenge nonce is ≥64 bits, statistically or lifetime unique. | CWE-330 |

### V6.8 Authentication with an Identity Provider

| ID | Level | Requirement | CWE |
|----|-------|-------------|-----|
| V6.8.1 | L2 | Multi-IdP: user identity is not spoofable via another IdP; use IdP+user ID combination. | CWE-287 |
| V6.8.2 | L2 | Digital signatures on authentication assertions are validated; unsigned assertions are rejected. | CWE-347 |
| V6.8.3 | L2 | SAML assertions are uniquely processed and used once within the validity period. | CWE-287 |
| V6.8.4 | L2 | Application validates specific authentication strength from IdP claims (`acr`/`amr`/`auth_time`). | — |

---

## V7: Session Management

### V7.1 Session Management Documentation

| ID | Level | Requirement | CWE |
|----|-------|-------------|-----|
| V7.1.1 | L2 | Session inactivity timeout and absolute maximum lifetime are documented, appropriate in combination with other controls, with justification for deviations from NIST SP 800-63B. | — |
| V7.1.2 | L2 | Documentation defines how many concurrent sessions are allowed per account and behavior when the maximum is reached. | — |
| V7.1.3 | L2 | All systems managing user sessions in a federated identity ecosystem are documented with session coordination controls. | — |

### V7.2 Fundamental Session Management Security

| ID | Level | Requirement | CWE |
|----|-------|-------------|-----|
| V7.2.1 | L1 | All session token verification is performed using a trusted backend service. | CWE-384 |
| V7.2.2 | L1 | Application uses dynamically generated self-contained or reference tokens for session management, not static API secrets and keys. | CWE-384 |
| V7.2.3 | L1 | Reference tokens representing user sessions are unique and generated using a CSPRNG with at least 128 bits of entropy. | CWE-330 |
| V7.2.4 | L1 | Application generates a new session token on user authentication (including re-authentication) and terminates the prior session token. | CWE-384 |

### V7.3 Session Timeout

| ID | Level | Requirement | CWE |
|----|-------|-------------|-----|
| V7.3.1 | L2 | Inactivity timeout exists, enforcing re-authentication per risk analysis and documented security decisions. | CWE-613 |
| V7.3.2 | L2 | Absolute maximum session lifetime exists, enforcing re-authentication per risk analysis. | CWE-613 |

### V7.4 Session Termination

| ID | Level | Requirement | CWE |
|----|-------|-------------|-----|
| V7.4.1 | L1 | When session termination is triggered (logout or expiration), the application disallows further session use. Reference/stateful sessions are invalidated at the backend; self-contained tokens require a revocation list, pre-date invalidation, or per-user key rotation. | CWE-384 |
| V7.4.2 | L1 | Application terminates all active sessions when a user account is disabled or deleted. | CWE-384 |
| V7.4.3 | L2 | Application offers the option to terminate all other active sessions after a successful change or removal of an authentication factor. | CWE-384 |
| V7.4.4 | L2 | All pages requiring authentication have easy, visible access to logout functionality. | CWE-384 |
| V7.4.5 | L2 | Application administrators can terminate active sessions for an individual user or all users. | CWE-384 |

### V7.5 Defenses Against Session Abuse

| ID | Level | Requirement | CWE |
|----|-------|-------------|-----|
| V7.5.1 | L2 | Full re-authentication is required before modifications to sensitive account attributes (email, phone, MFA configuration, account recovery). | CWE-384 |
| V7.5.2 | L2 | Users can view and (after re-authenticating with at least one factor) terminate any or all currently active sessions. | CWE-384 |
| V7.5.3 | L3 | Further authentication with at least one factor or secondary verification is required before performing highly sensitive transactions. | CWE-384 |

### V7.6 Federated Re-authentication

| ID | Level | Requirement | CWE |
|----|-------|-------------|-----|
| V7.6.1 | L2 | Session lifetime and termination between Relying Parties and Identity Providers behave as documented; re-authentication is required when maximum time between IdP authentication events is reached. | — |
| V7.6.2 | L2 | Session creation requires user consent or explicit action; sessions are not created silently without user interaction. | CWE-384 |

---

## V8: Authorization

### V8.1 Authorization Documentation

| ID | Level | Requirement | CWE |
|----|-------|-------------|-----|
| V8.1.1 | L1 | Documentation defines rules restricting function-level and data-specific access. | — |
| V8.1.2 | L2 | Documentation defines field-level access rules (read/write) based on permissions. | — |
| V8.1.3 | L3 | Documentation defines environmental factors (time, location, IP, device) in authorization decisions. | — |
| V8.1.4 | L3 | Documentation defines how environmental factors influence authentication and authorization decisions. | — |

### V8.2 General Authorization Design

| ID | Level | Requirement | CWE |
|----|-------|-------------|-----|
| V8.2.1 | L1 | Function-level access is restricted to explicitly permitted consumers. | CWE-285 |
| V8.2.2 | L1 | Data-specific access is restricted to prevent IDOR/BOLA. | CWE-639 |
| V8.2.3 | L2 | Field-level access is restricted to prevent BOPLA. | CWE-639 |
| V8.2.4 | L3 | Adaptive security controls based on environmental attributes are implemented. | — |

### V8.3 Operation Level Authorization

| ID | Level | Requirement | CWE |
|----|-------|-------------|-----|
| V8.3.1 | L1 | Authorization is enforced at a trusted service layer, not dependent on client-side controls. | CWE-602 |
| V8.3.2 | L3 | Changes affecting authorization decisions are applied immediately or mitigated. | — |
| V8.3.3 | L3 | Access is based on the subject's own permissions, not on intermediary or service permissions. | CWE-441 |

### V8.4 Other Authorization Considerations

| ID | Level | Requirement | CWE |
|----|-------|-------------|-----|
| V8.4.1 | L2 | Multi-tenant cross-tenant controls prevent unauthorized tenant interaction. | CWE-284 |
| V8.4.2 | L3 | Admin interfaces require continuous identity verification, device posture checks, and risk analysis. | — |

---

## V9: Self-contained Tokens

### V9.1 Token Source and Integrity

| ID | Level | Requirement | CWE |
|----|-------|-------------|-----|
| V9.1.1 | L1 | Self-contained tokens are validated using a digital signature or MAC before accepting the token's contents. | CWE-347 |
| V9.1.2 | L1 | Only allowlisted algorithms can create and verify self-contained tokens; the allowlist must not include the `None` algorithm. | CWE-347 |
| V9.1.3 | L1 | Key material for validating self-contained tokens comes from trusted pre-configured sources; JWS headers `jku`, `x5u`, `jwk` are validated against an allowlist. | CWE-347 |

### V9.2 Token Content

| ID | Level | Requirement | CWE |
|----|-------|-------------|-----|
| V9.2.1 | L1 | If a validity time span is present in token data, the token is accepted only if verification time is within the validity span (`nbf` and `exp` for JWTs). | CWE-613 |
| V9.2.2 | L2 | Token type is validated before accepting contents (only access tokens for authorization, ID tokens for authentication). | CWE-347 |
| V9.2.3 | L2 | Token audience (`aud` claim for JWTs) is validated against an allowlist to ensure the token is intended for this service. | CWE-347 |
| V9.2.4 | L2 | If the token issuer uses the same private key for different audiences, tokens contain an audience restriction uniquely identifying the intended audience. | CWE-347 |

---

## V10: OAuth and OIDC

### V10.1 Generic OAuth and OIDC Security

| ID | Level | Requirement | CWE |
|----|-------|-------------|-----|
| V10.1.1 | L2 | Tokens are only sent to components that strictly need them. Using a BFF pattern, access and refresh tokens are only accessible to the backend. | CWE-347 |
| V10.1.2 | L2 | Client only accepts values from the authorization server (authorization code or ID Token) if they resulted from an authorization flow initiated by the same user agent session. Requires non-guessable, transaction-specific, securely bound client-generated secrets (PKCE `code_verifier`, `state`, OIDC `nonce`). | CWE-347 |

### V10.2 OAuth Client

| ID | Level | Requirement | CWE |
|----|-------|-------------|-----|
| V10.2.1 | L2 | If code flow is used, the OAuth client has CSRF protection via PKCE or `state` parameter validation. | CWE-352 |
| V10.2.2 | L2 | If the OAuth client interacts with multiple authorization servers, it has defense against mix-up attacks (e.g., validating `iss` parameter). | CWE-346 |
| V10.2.3 | L3 | OAuth client only requests required scopes. | CWE-347 |

### V10.3 OAuth Resource Server

| ID | Level | Requirement | CWE |
|----|-------|-------------|-----|
| V10.3.1 | L2 | Resource server only accepts access tokens intended for that service (audience validation via JWT `aud` or introspection). | CWE-347 |
| V10.3.2 | L2 | Resource server enforces authorization decisions based on claims from the access token (`sub`, `scope`, `authorization_details`). | CWE-347 |
| V10.3.3 | L2 | If user identification is needed, the resource server uses non-reassignable claims (typically `iss` + `sub` combination). | CWE-347 |
| V10.3.4 | L2 | If specific authentication strength is required, the resource server verifies the access token satisfies constraints using OIDC `acr`/`amr`/`auth_time` claims. | CWE-347 |
| V10.3.5 | L3 | Resource server prevents stolen access token use by requiring sender-constrained tokens (mTLS or DPoP). | CWE-347 |

### V10.4 OAuth Authorization Server

| ID | Level | Requirement | CWE |
|----|-------|-------------|-----|
| V10.4.1 | L1 | Authorization server validates redirect URIs based on a client-specific allowlist using exact string comparison. | CWE-346 |
| V10.4.2 | L1 | Authorization code is usable only once; a second request with an already-used code is rejected and related tokens are revoked. | CWE-346 |
| V10.4.3 | L1 | Authorization code is short-lived: max 10 minutes for L1/L2, max 1 minute for L3. | CWE-613 |
| V10.4.4 | L1 | Only grants the client needs are allowed; Implicit (`token`) and Resource Owner Password Credentials (`password`) flows are not used. | CWE-346 |
| V10.4.5 | L1 | Refresh token replay is mitigated for public clients using sender-constrained tokens (DPoP, mTLS) or refresh token rotation with invalidation on reuse. | CWE-347 |
| V10.4.6 | L2 | PKCE is required for code grant; `code_challenge` must be valid, `plain` method is rejected, `code_verifier` is validated in token request. | CWE-347 |
| V10.4.7 | L2 | If supporting dynamic client registration, malicious clients are mitigated by validating client metadata and ensuring user consent. | CWE-346 |
| V10.4.8 | L2 | Refresh tokens have absolute expiration even if sliding refresh token expiration is applied. | CWE-613 |
| V10.4.9 | L2 | Refresh tokens and reference access tokens can be revoked by authorized users. | CWE-347 |
| V10.4.10 | L2 | Confidential clients are authenticated for backchannel requests (token requests, PAR, revocation). | CWE-347 |
| V10.4.11 | L2 | Authorization server configuration assigns only required scopes to each OAuth client. | CWE-347 |
| V10.4.12 | L3 | Only allowed `response_mode` values are accepted, validated against expected values or using PAR/JAR. | CWE-347 |
| V10.4.13 | L3 | Grant type `code` is always used together with Pushed Authorization Requests (PAR). | CWE-347 |
| V10.4.14 | L3 | Authorization server issues only sender-constrained (Proof-of-Possession) access tokens: mTLS or DPoP. | CWE-347 |
| V10.4.15 | L3 | For server-side clients, the authorization server ensures `authorization_details` parameter value hasn't been tampered with; requires PAR or JAR. | CWE-347 |
| V10.4.16 | L3 | Client uses strong authentication: mTLS or private key JWT. | CWE-347 |

### V10.5 OIDC Client

| ID | Level | Requirement | CWE |
|----|-------|-------------|-----|
| V10.5.1 | L2 | Client mitigates ID Token replay by ensuring `nonce` claim matches the `nonce` sent in the authentication request. | CWE-347 |
| V10.5.2 | L2 | Client uniquely identifies users from ID Token claims (usually `sub`) which cannot be reassigned. | CWE-347 |
| V10.5.3 | L2 | Client rejects authorization server metadata if issuer URL doesn't match the pre-configured expected issuer URL. | CWE-346 |
| V10.5.4 | L2 | Client validates ID Token is intended for that client by checking `aud` equals `client_id`. | CWE-347 |
| V10.5.5 | L2 | For OIDC back-channel logout, the relying party verifies the logout token is typed as `logout+jwt`, contains correct `event` claim, doesn't contain `nonce` claim, and has short expiration. | CWE-613 |

### V10.6 OpenID Provider

| ID | Level | Requirement | CWE |
|----|-------|-------------|-----|
| V10.6.1 | L2 | OpenID Provider only allows `response_mode` values `code`, `ciba`, `id_token`, `id_token code`. `code` is preferred. `token` (Implicit) is not used. | CWE-346 |
| V10.6.2 | L2 | OpenID Provider mitigates forced logout DoS by requiring explicit end-user confirmation or validating logout request parameters. | CWE-613 |

### V10.7 Consent Management

| ID | Level | Requirement | CWE |
|----|-------|-------------|-----|
| V10.7.1 | L2 | Authorization server ensures user consents to each authorization request; explicit consent prompt is required if client identity cannot be assured. | — |
| V10.7.2 | L2 | Consent prompt presents sufficient clear information about what is being consented to: scope, resource server, authorization details, application identity, authorization lifetime. | — |
| V10.7.3 | L2 | User can review, modify, and revoke consents granted through the authorization server. | — |
