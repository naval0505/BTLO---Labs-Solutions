# BTLO - JWT Security Challenge Writeup

## Basic Information

| Category | Details |
|----------|----------|
| Platform | Blue Team Labs Online (BTLO) |
| Challenge | JWT Security Challenge |
| Difficulty | Easy |
| Category | Web Security / Authentication / JSON Web Tokens |
| Focus | JWT Analysis, Secret Cracking, Token Forgery |

---

# Introduction

Today we are solving another **Blue Team Labs Online (BTLO)** challenge focused on **JSON Web Token (JWT) security**.

Unlike traditional defensive challenges involving malware analysis or log investigation, this challenge focuses on understanding how authentication tokens work and how weak cryptographic secrets can allow attackers to forge valid authentication tokens.

Throughout this challenge we analyze an intercepted JWT, identify sensitive information contained within it, recover the signing secret, and finally generate a modified token with reduced privileges.

This challenge highlights the importance of strong secret management and secure JWT implementations.

---

# Scenario

The provided scenario states:

> You're a senior cyber security engineer and during your shift, we have intercepted a high-privilege authentication token that appears to have been abused. Since you originally created the signing secret, your task is to recover it, modify the token into a low-privilege version, and submit the corrected ticket.

The intercepted JWT is provided as:

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJmbGFnIjoiQlRMe180X0V5ZXN9IiwiaWF0Ijo5MDAwMDAwMCwibmFtZSI6IkdyZWF0RXhwIiwiYWRtaW4iOnRydWV9.jbkZHll_W17BOALT95JQ17glHBj9nY-oWhT1uiahtv8
```

---

# Objectives

During this assessment we aim to:

- Understand the structure of a JWT.
- Decode the intercepted authentication token.
- Analyze the embedded claims.
- Recover useful information from the payload.
- Identify the hidden challenge hint.
- Prepare for recovering the signing secret.
- Forge a modified token.

---

# What is a JSON Web Token (JWT)?

A **JSON Web Token (JWT)** is a compact authentication token commonly used in modern web applications for securely transmitting user information between a client and a server.

Instead of storing session data on the server, applications often issue JWTs containing user information and digitally sign them to ensure integrity.

JWTs are commonly used for:

- Authentication
- Authorization
- Single Sign-On (SSO)
- API Security
- Identity Management

---

# JWT Structure

Every JWT consists of three Base64URL encoded components separated by periods.

```
Header.Payload.Signature
```

Visual representation:

```
┌──────────┐
│  Header  │
└──────────┘
      │
      ▼
┌──────────┐
│ Payload  │
└──────────┘
      │
      ▼
┌──────────┐
│Signature │
└──────────┘
```

Example:

```
xxxxx.yyyyy.zzzzz
```

Each section serves a unique purpose.

---

# Header Analysis

The first section of the token contains metadata describing how the token was generated.

Decoding the first segment reveals:

```json
{
  "typ": "JWT",
  "alg": "HS256"
}
```

Meaning:

| Field | Description |
|--------|-------------|
| typ | Token type |
| alg | Signing algorithm |

The algorithm used is:

```
HS256
```

HS256 uses an HMAC combined with SHA-256 and relies on a shared secret known by both the client and server.

---

# Payload Analysis

The second section contains the application data (claims).

Decoding the payload reveals:

```json
{
  "flag": "BTL{_4_Eyes}",
  "iat": 90000000,
  "name": "GreatExp",
  "admin": true
}
```

This section contains several important claims.

---

# Understanding the Claims

### flag

```
BTL{_4_Eyes}
```

A challenge flag intentionally embedded by BTLO.

---

### iat

```
90000000
```

Represents the **Issued At** timestamp.

Applications use this claim to determine when the token was created.

---

### name

```
GreatExp
```

Identifies the authenticated user.

---

### admin

```
true
```

This claim determines authorization.

Current privileges:

```
Administrator
```

Since the challenge requests a **low privilege** ticket, this claim will eventually need to become:

```
false
```

---

# Security Observation

One important characteristic of JWTs is that:

```
JWTs are encoded

NOT

encrypted.
```

Anyone possessing the token can decode:

- usernames
- timestamps
- roles
- flags
- identifiers

without knowing the secret key.

Only the **signature** prevents unauthorized modification.

---

# Extracting the Challenge Hint

While reviewing the payload, one field immediately stands out.

```
flag

↓

BTL{_4_Eyes}
```

The hidden hint becomes:

```
_4_Eyes
```

This answers the third challenge question.

Although it appears to be a flag, it also serves as a clue guiding the remainder of the challenge.

---

# Question Walkthrough

## Question 1

**Can you identify the name of the token?**

Answer:

```
JWT
```

---

## Question 2

**What is the structure of this token?**

Answer:

```
Header.Payload.Signature
```

---

## Question 3

**What is the hint found inside the token?**

After decoding the payload:

```json
{
  "flag": "BTL{_4_Eyes}"
}
```

Answer:

```
_4_Eyes
```

---

# Key Takeaways

At this stage we have successfully:

- Identified the authentication mechanism.
- Learned the structure of a JWT.
- Decoded the intercepted token.
- Understood the header fields.
- Examined each payload claim.
- Identified the signing algorithm.
- Extracted the embedded challenge hint.
- Determined that the current token grants administrator privileges.

The next phase focuses on recovering the **HS256 signing secret**, demonstrating how weak secrets can be cracked using dictionary attacks, and finally forging a new **low-privilege JWT** with a valid signature.

# Recovering the JWT Secret

After analyzing the JWT structure and decoding its contents, the next objective is determining the secret key used to generate the token's signature.

Since the token uses the **HS256** signing algorithm, both the server and the client rely on the same shared secret.

If an attacker successfully discovers this secret, they can:

- Forge authentication tokens
- Escalate privileges
- Modify existing claims
- Bypass authentication controls

Recovering this secret therefore becomes the most important step in the challenge.

---

# Understanding HS256

The decoded header revealed:

```json
{
  "alg": "HS256"
}
```

HS256 stands for:

```
HMAC

+

SHA-256
```

Unlike asymmetric algorithms such as **RS256**, HS256 relies on a single shared secret.

```
Client
        │
        ▼
Shared Secret
        │
        ▼
Server
```

Anyone who knows this secret can generate completely valid JWTs.

---

# Dictionary Attack

Instead of attempting an impossible brute-force attack against every possible key, a dictionary attack is performed using **jwt_tool**.

Command used:

```bash
python3 jwt_tool.py '<JWT_TOKEN>' -C -d /home/kabir/tools/jwt.secrets.list
```

Where:

```
-C

↓

Crack Mode
```

and

```
-d

↓

Dictionary File
```

The tool automatically attempts every candidate secret until the generated signature matches the intercepted JWT.

---

# Cracking the Secret

After processing the supplied wordlist, the tool reports:

```
[+] bT!0 is the CORRECT key!
```

The recovered signing secret is:

```
bT!0
```

This answers the fourth challenge question.

At this point the attacker possesses everything required to generate valid JWTs.

---

# Why Weak Secrets Are Dangerous

JWT security depends entirely on the strength of the signing secret.

Weak secrets create several risks:

- Token forgery
- Privilege escalation
- Authentication bypass
- Identity spoofing
- Complete application compromise

Even though the signature uses SHA-256, a predictable secret dramatically weakens the overall security.

---

# Forging a New Token

The challenge requires generating a **low-privilege** token.

Reviewing the decoded payload shows:

```json
{
  "admin": true
}
```

To satisfy the challenge requirements, this value is modified.

```
true

↓

false
```

All remaining claims remain unchanged.

---

# Modified Payload

The updated payload becomes:

```json
{
  "flag": "BTL{_4_Eyes}",
  "iat": 90000000,
  "name": "GreatExp",
  "admin": false
}
```

Although only one field changes, the entire token signature must be regenerated.

---

# Signing the Token

Because the signing secret is now known, a completely valid JWT can be generated.

The modified payload is signed using:

```
HS256

Secret

↓

bT!0
```

The resulting JWT is accepted as authentic because the signature matches the modified contents.

---

# Final Low-Privilege Token

The newly generated token is:

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJmbGFnIjoiQlRMe180X0V5ZXN9IiwiaWF0Ijo5MDAwMDAwMCwibmFtZSI6IkdyZWF0RXhwIiwiYWRtaW4iOmZhbHNlfQ.nMXNFvttCvtDcpswOQA8u_LpURwv6ZrCJ-ftIXegtX4
```

This successfully completes the challenge by replacing the administrator ticket with a valid low-privilege version.

---

# Complete Attack Flow

```
Intercept JWT
       │
       ▼
Decode Header
       │
       ▼
Decode Payload
       │
       ▼
Extract Claims
       │
       ▼
Identify HS256
       │
       ▼
Dictionary Attack
       │
       ▼
Recover Secret
       │
       ▼
Modify Payload
       │
       ▼
Re-sign JWT
       │
       ▼
Generate Low-Privilege Token
```

---

# Vulnerabilities Identified

## 1. Weak JWT Secret

The signing secret was sufficiently weak to be recovered using a dictionary attack.

### Impact

- Token forgery
- Authentication bypass
- Privilege escalation
- Identity spoofing

### Mitigation

- Use randomly generated secrets of at least 256 bits.
- Rotate signing secrets regularly.
- Store secrets securely using a dedicated secrets management solution.

---

## 2. Predictable Authentication Design

The application relied entirely on a shared secret for authentication integrity.

### Impact

- Complete compromise if the secret is exposed.

### Mitigation

- Prefer asymmetric algorithms such as **RS256** or **ES256** where appropriate.
- Separate signing and verification responsibilities.

---

## 3. Client-Controlled Authorization Claims

The JWT directly stored the user's authorization level.

```json
"admin": true
```

Although protected by the signature, compromise of the signing key allows arbitrary privilege changes.

### Impact

- Administrative access
- Privilege escalation
- Unauthorized actions

### Mitigation

- Validate critical authorization decisions against the server.
- Avoid relying solely on client-provided role claims.

---

# MITRE ATT&CK Mapping

| Technique | ATT&CK ID |
|------------|-----------|
| Exploit Public-Facing Application | T1190 |
| Brute Force (Dictionary Attack) | T1110 |
| Steal or Forge Authentication Material | T1649 |
| Valid Accounts | T1078 |

---

# Detection Opportunities

Defenders should monitor for:

- Multiple failed JWT signature validations.
- Repeated requests containing modified JWTs.
- Sudden privilege changes within authentication tokens.
- High volumes of authentication failures from a single source.
- Use of expired or tampered tokens.
- Abnormal access to privileged resources immediately after token changes.

Implementing centralized logging and anomaly detection can help identify attempts to abuse JWT-based authentication.

---

# Security Recommendations

To strengthen JWT implementations:

- Use long, cryptographically random signing secrets.
- Rotate secrets periodically.
- Prefer asymmetric signing algorithms (RS256/ES256) where appropriate.
- Avoid storing sensitive authorization logic entirely within JWT claims.
- Validate important privileges against server-side data.
- Enforce short token lifetimes and refresh mechanisms.
- Monitor authentication events for signs of token tampering.

---

# Lessons Learned

This challenge demonstrates that the security of a JSON Web Token depends not only on the signing algorithm but also on the strength of the secret protecting it. Although HS256 provides strong cryptographic integrity, a weak or predictable signing key allows attackers to recover the secret through dictionary attacks and generate completely valid forged tokens. Once the signing secret is compromised, attackers can freely modify authorization claims such as administrative privileges without triggering signature validation failures.

The exercise reinforces the importance of strong secret management, careful selection of signing algorithms, and server-side validation of sensitive authorization decisions. Secure authentication requires more than simply using JWTs—it requires protecting the cryptographic keys that make those tokens trustworthy.

---

# Conclusion

The **JWT Security Challenge** from **Blue Team Labs Online** provides an excellent introduction to JSON Web Token security and the risks associated with weak authentication secrets. Starting with an intercepted administrator token, the challenge demonstrated how JWTs can be decoded, analyzed, attacked through dictionary-based secret recovery, and ultimately forged into a new valid token with modified privileges.

The challenge highlights essential concepts in modern web application security, including JWT structure, HMAC-based signing, secret management, and token integrity. It also serves as a practical reminder that even strong cryptographic algorithms become ineffective when paired with weak secrets or poor key management practices.
