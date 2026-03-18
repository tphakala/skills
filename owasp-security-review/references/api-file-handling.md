# V4: API and Web Service + V5: File Handling

OWASP ASVS 5.0 requirements for API security, HTTP message handling, GraphQL, WebSockets, and file upload/download/storage.

## Table of Contents

- [V4.1 Generic Web Service Security](#v41-generic-web-service-security)
- [V4.2 HTTP Message Structure Validation](#v42-http-message-structure-validation)
- [V4.3 GraphQL](#v43-graphql)
- [V4.4 WebSocket](#v44-websocket)
- [V5.1 File Handling Documentation](#v51-file-handling-documentation)
- [V5.2 File Upload and Content](#v52-file-upload-and-content)
- [V5.3 File Storage](#v53-file-storage)
- [V5.4 File Download](#v54-file-download)

---

## V4.1 Generic Web Service Security

| ID | Level | Requirement | CWE |
|----|-------|-------------|-----|
| V4.1.1 | L1 | Every HTTP response with a message body contains a `Content-Type` header matching the actual content, with a `charset` parameter per IANA Media Types. | CWE-200 |
| V4.1.2 | L2 | User-facing endpoints automatically redirect HTTP to HTTPS. Other services do not implement transparent redirects (to prevent undetected unencrypted requests). | CWE-295 |
| V4.1.3 | L2 | HTTP header fields set by intermediary layers (load balancer, web proxy, BFF) cannot be overridden by end-users. Examples: `X-Real-IP`, `X-Forwarded-*`. | CWE-444 |
| V4.1.4 | L3 | Only explicitly supported HTTP methods (including OPTIONS during preflight) can be used; unused methods are blocked. | CWE-405 |
| V4.1.5 | L3 | Per-message digital signatures are used for highly sensitive requests or transactions traversing multiple systems. | — |

## V4.2 HTTP Message Structure Validation

| ID | Level | Requirement | CWE |
|----|-------|-------------|-----|
| V4.2.1 | L2 | All application components determine incoming HTTP message boundaries correctly for the HTTP version to prevent request smuggling. HTTP/1.x must ignore `Content-Length` if `Transfer-Encoding` is present. HTTP/2+ must ensure `Content-Length` consistency with DATA frames. | CWE-444 |
| V4.2.2 | L3 | `Content-Length` header field doesn't conflict with content length determined by HTTP protocol framing. | CWE-444 |
| V4.2.3 | L3 | Application doesn't send or accept HTTP/2 or HTTP/3 messages with connection-specific header fields like `Transfer-Encoding`. | CWE-230 |
| V4.2.4 | L3 | Application only accepts HTTP/2 and HTTP/3 requests without CR, LF, or CRLF sequences in header fields and values. | CWE-230 |
| V4.2.5 | L3 | Application uses validation, sanitization, or other mechanisms to avoid creating overly long URIs or HTTP request header fields that could cause denial of service. | CWE-400 |

## V4.3 GraphQL

| ID | Level | Requirement | CWE |
|----|-------|-------------|-----|
| V4.3.1 | L2 | Query allowlist, depth limiting, amount limiting, or query cost analysis is used to prevent GraphQL DoS from expensive nested queries. | CWE-770 |
| V4.3.2 | L2 | GraphQL introspection queries are disabled in production unless the API is meant to be used by other parties. | CWE-200 |

## V4.4 WebSocket

| ID | Level | Requirement | CWE |
|----|-------|-------------|-----|
| V4.4.1 | L1 | WebSocket over TLS (WSS) is used for all WebSocket connections. | CWE-319 |
| V4.4.2 | L2 | During the initial HTTP WebSocket handshake, the `Origin` header field is checked against a list of allowed origins. | CWE-346 |
| V4.4.3 | L2 | If the application's standard session management cannot be used for WebSocket, dedicated tokens are used that comply with session management security requirements. | — |
| V4.4.4 | L2 | Dedicated WebSocket session management tokens are initially obtained or validated through a previously authenticated HTTPS session. | — |

---

## V5.1 File Handling Documentation

| ID | Level | Requirement | CWE |
|----|-------|-------------|-----|
| V5.1.1 | L2 | Documentation defines permitted file types, expected extensions, maximum file size (including unpacked), and specifies how files are made safe for end-users. | — |

## V5.2 File Upload and Content

| ID | Level | Requirement | CWE |
|----|-------|-------------|-----|
| V5.2.1 | L1 | Application only accepts file sizes it can process without performance degradation or denial of service. | CWE-400 |
| V5.2.2 | L1 | When accepting a file (standalone or within archive), application checks that file extension matches expected type and validates contents correspond to type via magic bytes, image rewriting, or specialized libraries. L1: business/security-critical files. L2+: all files. | CWE-434 |
| V5.2.3 | L2 | Compressed files (zip, gz, docx, odt) are checked against maximum allowed uncompressed size and maximum number of files before decompression. | CWE-409 |
| V5.2.4 | L3 | File size quota and maximum file count per user are enforced to prevent single-user storage exhaustion. | CWE-770 |
| V5.2.5 | L3 | Application does not allow uploading compressed files with symlinks unless specifically required (with allowlist of symlink targets). | CWE-22 |
| V5.2.6 | L3 | Application rejects uploaded images with pixel dimensions larger than maximum allowed to prevent pixel flood attacks. | CWE-770 |

## V5.3 File Storage

| ID | Level | Requirement | CWE |
|----|-------|-------------|-----|
| V5.3.1 | L1 | Files uploaded by untrusted input are stored so they are not executed as server-side code when accessed directly via HTTP. | CWE-434 |
| V5.3.2 | L1 | File paths use internally generated or trusted data instead of user-submitted filenames. If user filenames must be used, strict validation and sanitization protect against path traversal, LFI, RFI, and SSRF. | CWE-22 |
| V5.3.3 | L3 | Server-side file processing (decompression) ignores user-provided path information to prevent zip slip vulnerabilities. | CWE-22 |

## V5.4 File Download

| ID | Level | Requirement | CWE |
|----|-------|-------------|-----|
| V5.4.1 | L2 | Application validates or ignores user-submitted filenames and specifies the filename in the `Content-Disposition` header in the response. | CWE-434 |
| V5.4.2 | L2 | File names in HTTP headers or email attachments are encoded or sanitized per RFC 6266. | CWE-434 |
| V5.4.3 | L2 | Files from untrusted sources are scanned by antivirus scanners to prevent serving known malicious content. | CWE-434 |
