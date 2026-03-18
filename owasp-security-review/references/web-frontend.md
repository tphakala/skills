# V3: Web Frontend Security

OWASP ASVS 5.0 requirements for browser-side security: content rendering, cookies, security headers, origin separation, and external resources.

## Table of Contents

- [V3.1 Web Frontend Security Documentation](#v31-web-frontend-security-documentation)
- [V3.2 Unintended Content Interpretation](#v32-unintended-content-interpretation)
- [V3.3 Cookie Setup](#v33-cookie-setup)
- [V3.4 Browser Security Mechanism Headers](#v34-browser-security-mechanism-headers)
- [V3.5 Browser Origin Separation](#v35-browser-origin-separation)
- [V3.6 External Resource Integrity](#v36-external-resource-integrity)
- [V3.7 Other Browser Security Considerations](#v37-other-browser-security-considerations)

---

## V3.1 Web Frontend Security Documentation

| ID | Level | Requirement | CWE |
|----|-------|-------------|-----|
| V3.1.1 | L3 | Application documentation states the expected browser security features (HTTPS, HSTS, CSP) and defines behavior when these features are unavailable. | — |

## V3.2 Unintended Content Interpretation

| ID | Level | Requirement | CWE |
|----|-------|-------------|-----|
| V3.2.1 | L1 | Security controls prevent browsers from rendering content in incorrect context; uses `Sec-Fetch-*`, CSP sandbox, or `Content-Disposition: attachment`. | CWE-345 |
| V3.2.2 | L1 | Text content uses safe rendering functions (`createTextNode`, `textContent`) to prevent unintended execution. | CWE-79 |
| V3.2.3 | L3 | DOM clobbering is avoided through explicit variable declarations, strict type checking, and not relying on global document variables. | CWE-79 |

## V3.3 Cookie Setup

| ID | Level | Requirement | CWE |
|----|-------|-------------|-----|
| V3.3.1 | L1 | Cookies have the `Secure` attribute set. If `__Host-` prefix is not used, `__Secure-` prefix must be used. | CWE-614 |
| V3.3.2 | L2 | Cookie `SameSite` attribute is configured per purpose to limit UI redress and CSRF attacks. | CWE-352 |
| V3.3.3 | L2 | Cookies use `__Host-` prefix unless explicitly designed for host sharing. | CWE-614 |
| V3.3.4 | L2 | If cookie value is not meant for client-side scripts (e.g., session token), cookie has `HttpOnly` attribute and value is transferred only via `Set-Cookie` header. | CWE-614 |
| V3.3.5 | L3 | Combined cookie name and value length does not exceed 4096 bytes. | — |

## V3.4 Browser Security Mechanism Headers

| ID | Level | Requirement | CWE |
|----|-------|-------------|-----|
| V3.4.1 | L1 | `Strict-Transport-Security` header included on all responses with `max-age` of at least 1 year. L2+ must apply to all subdomains (`includeSubDomains`). | CWE-319 |
| V3.4.2 | L1 | CORS `Access-Control-Allow-Origin` is a fixed value or validated against an allowlist. If `*` is used, no sensitive information is in the response. | CWE-346 |
| V3.4.3 | L2 | HTTP responses include `Content-Security-Policy` with directives ensuring the browser only loads trusted content. Minimum: `object-src 'none'` and `base-uri 'none'`. L3 requires per-response policy with nonces or hashes. | CWE-79 |
| V3.4.4 | L2 | All HTTP responses contain `X-Content-Type-Options: nosniff`. | CWE-432 |
| V3.4.5 | L2 | `Referrer-Policy` header prevents leakage of sensitive data via `Referer` header. | CWE-200 |
| V3.4.6 | L2 | `frame-ancestors` directive in CSP prevents embedding by default. (`X-Frame-Options` is obsolete.) | CWE-1021 |
| V3.4.7 | L3 | CSP header specifies a `report-uri` or `report-to` location for violation reporting. | — |
| V3.4.8 | L3 | `Cross-Origin-Opener-Policy` header uses `same-origin` or `same-origin-allow-popups` to prevent tabnabbing and frame counting. | CWE-345 |

## V3.5 Browser Origin Separation

| ID | Level | Requirement | CWE |
|----|-------|-------------|-----|
| V3.5.1 | L1 | If the application does not rely on CORS preflight, it validates that requests originate from the application itself using anti-forgery tokens or non-CORS-safelisted headers. | CWE-352 |
| V3.5.2 | L1 | If relying on CORS preflight for sensitive functionality, non-preflight requests are prevented by checking `Origin` or `Content-Type` header values or requiring a non-safelisted header. | CWE-352 |
| V3.5.3 | L1 | Sensitive functionality uses `POST`, `PUT`, `PATCH`, or `DELETE` rather than `GET`/`HEAD`/`OPTIONS`. Alternatively, `Sec-Fetch-*` headers are validated. | CWE-352 |
| V3.5.4 | L2 | Separate applications are hosted on different hostnames to leverage same-origin policy restrictions. | — |
| V3.5.5 | L2 | `postMessage` interface discards messages if origin is untrusted or message syntax is invalid. | CWE-345 |
| V3.5.6 | L3 | JSONP functionality is disabled across the application to avoid XSSI attacks. | CWE-79 |
| V3.5.7 | L3 | Data requiring authorization is not included in script resource responses (e.g., JavaScript files). | CWE-79 |
| V3.5.8 | L3 | Authenticated resources (images, videos, scripts, documents) are only loadable when intended; validates `Sec-Fetch-*` headers or sets restrictive `Cross-Origin-Resource-Policy`. | CWE-345 |

## V3.6 External Resource Integrity

| ID | Level | Requirement | CWE |
|----|-------|-------------|-----|
| V3.6.1 | L3 | Client-side assets externally hosted are only allowed if static, versioned, and use Subresource Integrity (SRI). Otherwise, this is a documented security decision. | CWE-345 |

## V3.7 Other Browser Security Considerations

| ID | Level | Requirement | CWE |
|----|-------|-------------|-----|
| V3.7.1 | L2 | Application uses only supported, secure client-side technologies. No NSAPI plugins, Flash, Shockwave, ActiveX, Silverlight, NaCl, or Java applets. | — |
| V3.7.2 | L2 | Application only automatically redirects to non-controlled hostnames if the destination is on an allowlist. | CWE-601 |
| V3.7.3 | L3 | A notification is shown when the user is redirected outside the application's control, with an option to cancel navigation. | CWE-601 |
| V3.7.4 | L3 | Top-level domain is added to the public HSTS preload list. | CWE-319 |
| V3.7.5 | L3 | Application behaves as documented (warning or blocking access) if the browser doesn't support expected security features. | — |
