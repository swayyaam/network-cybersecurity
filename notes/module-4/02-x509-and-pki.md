# X.509 and PKI

> **Source:** `Module 4/4.2 Authentication_X 509.pptx`, slides 3–16 · **Syllabus:** Unit IV

## Key points

- **X.509** defines the **format of public-key certificates** and authentication protocols; it recommends **RSA** and assumes a hash function.
- A **certificate** binds a public key to a user and is **signed by a trusted Certification Authority (CA)**.
- A **PKI** is the hardware, software, people, policies and procedures to create, manage, store, distribute and **revoke** certificates.
- Authentication can be **one-way, two-way or three-way**; three-way needs **no synchronized clocks**.

---

## 1. Certificates

- A **CA** creates each user's certificate; the **directory** (X.500) only stores it for easy access.
- Anyone with the **CA's public key** can verify a certificate. Nobody but the CA can alter it undetected, so certificates are safe to keep in an open directory.

**Fields:**

| Field | Meaning |
|---|---|
| **Version** | Format version: 1 by default; **2** if unique IDs are present; **3** if extensions are present |
| **Serial number** | Integer unique within the issuing CA |
| **Signature algorithm ID** | Algorithm used to sign (repeated in the signature, so of little use) |
| **Issuer name** | X.500 name of the CA |
| **Validity period** | First and last valid dates |
| **Subject name** | The user the certificate refers to |
| **Subject public-key info** | The public key, its algorithm and parameters |
| **Issuer / subject unique ID** | Optional; for reused names (v2, rarely used) |
| **Extensions** | Extra fields (v3) |
| **Signature** | **Hash of all other fields, encrypted with the CA's private key** |

## 2. Revocation

A new certificate is normally issued before the old one expires. A certificate is **revoked early** if:

- the user's **private key is compromised**,
- the user is **no longer certified** by this CA, or
- the **CA's own certificate is compromised**.

## 3. Authentication procedures

All use **public-key signatures**; each party knows the other's public key (from the directory or sent in the first message).

| Type | Messages | Establishes |
|---|---|---|
| **One-way** | A → B | A's identity and that A sent it; it was meant for B; integrity and originality (not replayed) |
| **Two-way** | A ↔ B | The above, plus B's identity, that the reply was meant for A, and the reply's integrity and originality |
| **Three-way** | A → B, B → A, A → B | Adds a final message from A with a **signed copy of B's nonce (rB)**. Each side checks the returned nonce for replay, so **timestamps are not needed** (use when clocks aren't synchronized) |

## 4. Applications

Digital signatures on documents, **TLS/SSL** certificates (HTTPS), **S/MIME** email, **code signing**, certificate-based **SSH** authentication in some environments, and digital identities for users, devices and services.
