# JWT User Helper — Lightweight JWT Utilities for Node.js

JWT User Helper is a small, well-documented NPM package that provides reusable utilities and middleware to create, verify, and manage JSON Web Tokens (JWTs) for user authentication in Node.js applications. It focuses on secure token handling, clean Express middleware integration, and practical helpers for common auth patterns (access tokens, refresh tokens, role checks, and token revocation).

## Highlights & Features
- Easy-to-use token creation and verification helpers (signToken, verifyToken).
- Built-in Express middleware for authenticating requests and injecting user info into req.user.
- Support for both access tokens and refresh tokens with configurable expirations.
- Optional refresh token rotation helper and refresh-token endpoint patterns.
- Role and permission check middleware (e.g., requireRole, requireAnyRole).
- Token revocation support via in-memory or Redis-backed blacklist (pluggable store).
- Configurable signing algorithms (HS256, RS256) and key management hooks.
- TypeScript typings for strong DX (if package provides .d.ts or TS sources).
- Small footprint, zero-dependency core (optional adapters for Redis).
- Example usage, tests and clear migration guidance for secret/key rotation.

## Technologies & Tools
- Node.js (v14+ recommended)
- JavaScript or TypeScript (package supports TS types)
- jsonwebtoken (or compatible JWT library)
- Express (middleware integration examples)
- Redis (optional, for token revocation/state)
- Testing: Jest / Mocha + Supertest (examples)
- Packaging: npm, semantic-release (optional)
- CI: GitHub Actions (recommended for tests & linting)

## Skills & Expertise Demonstrated
- Authentication and authorization design using JWT
- Secure token lifecycle management (access vs refresh tokens)
- Express middleware patterns and composition
- Key/secret management and algorithm selection (symmetric vs asymmetric)
- Designing pluggable stores (in-memory vs Redis) for revocation and state
- TypeScript declaration and developer ergonomics
- Unit and integration testing for auth flows
- Documentation and example-driven DX for library consumers

## Challenges encountered and how to overcome them
1. Token theft and replay attacks
   - Mitigation: Use short-lived access tokens + refresh tokens, send tokens in secure HttpOnly cookies, implement refresh token rotation and bind refresh tokens to device/session IDs, and store revoked JTIs in a fast store (Redis).

2. Secret/key rotation without invalidating all active sessions
   - Mitigation: Support key identifiers (kid) in token headers and maintain a key set (rotate by adding new key and deprecating old). Verify tokens against multiple keys during a transition period and expire old keys after grace period.

3. Determining token revocation strategy at scale
   - Mitigation: Provide a pluggable revocation store interface; default to in-memory for small deployments and Redis for production. Use JTI claim for fast lookups and TTL-based revocation entries to avoid unbounded storage growth.

4. Handling clock skew and expiry edge cases
   - Mitigation: Allow a configurable clock skew tolerance (e.g., 30s) during verification and enforce nbf/iat/exp claims properly. Document recommended server time synchronization (NTP).

5. Securely storing refresh tokens in clients
   - Mitigation: Recommend secure HttpOnly SameSite cookies for browser clients, and secure storage mechanisms for native/mobile apps. Educate consumers about CSRF protections when using cookies.

6. Supporting both symmetric and asymmetric signing
   - Mitigation: Provide configuration for algorithm, public/private key inputs, and clear examples for HS* vs RS*/ES* setups. Offer helper utilities to load keys from files or environment variables.

## Real-world use cases
- Single Page Applications (SPAs) — use short-lived access tokens in memory and refresh tokens in HttpOnly cookies for session continuity.
- Mobile & Native Apps — issue refresh tokens bound to device IDs and rotate tokens on use to reduce compromise windows.
- Microservices — use JWTs for stateless service-to-service authentication with token verification middleware in each service.
- B2B APIs — create scoped tokens with custom claims for granular permissioning and auditing.
- Temporary access or invitation flows — mint tokens with specific claims and short lifetimes for one-off operations.
- Hybrid session systems — combine JWT access tokens with server-side refresh session stores to enable invalidation and session management.
- OAuth-like flows — use the helper to sign access/refresh tokens after an OAuth or SSO handshake.
