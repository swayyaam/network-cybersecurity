# E-Mail Security

> **Source:** `Module 4/4.6 E-Mail Security.pptx`, slides 2–28 (mostly image slides, transcribed) · **Syllabus:** Unit IV

## Key points

- E-mail moves via **SMTP** between servers and is read via **POP3/IMAP**; messages follow **RFC 5322** (header + blank line + body).
- SMTP only handles **7-bit ASCII text**; **MIME** extends it for binary and non-English data.
- Threats fall under authenticity, confidentiality, integrity and availability.
- Defenses: **STARTTLS** (server-to-server), **S/MIME** (message body), **SPF/DKIM/DMARC** (sender domain), **DNSSEC/DANE** (DNS and keys).

---

## 1. E-mail architecture

Parts of the **Message Handling System (MHS)**:

| Agent | Role |
|---|---|
| **MUA** (user agent) | The user's mail client |
| **MSA** (submission agent) | Accepts mail from the MUA (ESMTP submission) |
| **MTA** (transfer agent) | Relays mail hop-to-hop using SMTP |
| **MDA** (delivery agent) | Delivers to the recipient's message store |
| **MS** (message store) | Holds mail until read via IMAP/POP |

Path: sender MUA → MSA → MTA → … → MTA → MDA → MS → recipient MUA.

An **ADMD** (Administrative Management Domain, RFC 5598) is the boundary within which one organization controls mail handling: its servers, MTAs and policies (SPF, DKIM, DMARC). Types: mail service providers (Gmail, Outlook), enterprise IT groups, ISPs.

## 2. Protocols

| Protocol | Purpose | Key facts |
|---|---|---|
| **SMTP** | Moves mail **between servers across the internet** | Text-based client–server; RFC 821 (1982) → **RFC 5321** (2008); later versions called **ESMTP**. The client names the sender and recipient, then sends the message |
| **POP3** | A client **downloads** mail from a server | TCP **port 110**; after authorization the client can retrieve and delete mail |
| **IMAP** | A client **reads mail on the server** | TCP **port 143**; stronger authentication than POP3; **multiple devices** can access mail; users can create or update mail on the server |

**Format (RFC 5322):** header lines (`Keyword: arguments`, e.g. Date, From, Subject, To, Cc), a **blank line**, then the body.

**SMTP limits:** cannot send binary/executables; servers may reject large messages; **7-bit ASCII only** (no Unicode); inconsistent ASCII↔EBCDIC translation; X.400 gateways cannot carry non-text data; implementations may deviate from the standard. **MIME** (RFC 2045–2049) extends RFC 5322 to fix these.

## 3. Threats and mitigation

| Class | Risk |
|---|---|
| **Authenticity** | Unauthorized access to the enterprise mail system |
| **Confidentiality** | Disclosure of sensitive information |
| **Integrity** | Unauthorized changes to content |
| **Availability** | Users cannot send or receive |

| Threat | Mitigation |
|---|---|
| Mail from an **unauthorized MTA** (e.g. botnet), **spoofed or unregistered domain**, or **forged address** (phishing, spear phishing); hurts the sender's reputation and delivers spam/malicious links | **Domain-based authentication**; digital signatures |
| **Modified in transit**, or **sensitive data (PII) captured** by monitoring | **TLS** between servers; **end-to-end encryption** |
| **Unsolicited bulk e-mail (spam)** | Anti-spam techniques |
| **DoS/DDoS** on mail servers | Multiple mail servers; cloud mail providers |

## 4. Protocols that counter threats

| Protocol | What it does |
|---|---|
| **STARTTLS** (RFC 3207) | SMTP extension that runs **SMTP over TLS** between servers, giving confidentiality and authentication; one port serves both secure and plain operation |
| **S/MIME** | Authentication, integrity, non-repudiation and confidentiality of the **message body** (see §5) |
| **DNSSEC** | Authenticates and protects the integrity of **DNS data** |
| **DANE** | Authenticates public keys through DNSSEC, as an alternative to the CA system |
| **SPF** | DNS records listing the **IP ranges allowed to send** for a domain |
| **DKIM** | The MTA **signs selected headers and the body**; validates the source domain and body integrity |
| **DMARC** | Tells senders how well SPF/DKIM work and tells receivers **what to do** with failing mail |

## 5. S/MIME

A security enhancement to MIME giving four services:

| Function | Algorithm | Action |
|---|---|---|
| **Digital signature** (authentication) | RSA / SHA-256 | Hash the message with SHA-256; encrypt the digest with the **sender's private key** |
| **Message encryption** (confidentiality) | AES-128 CBC | Encrypt with a **one-time session key**; encrypt that key with the **recipient's public key (RSA)** and attach it |
| **Compression** | unspecified | Optional, for storage or transmission |
| **E-mail compatibility** | Radix-64 (Base64) | Turns 8-bit binary into printable 7-bit ASCII: **3 bytes → 4 characters** |

- **Authentication:** RSA assures only the private-key holder could sign; SHA-256 assures nobody can craft another message with the same hash. **Detached signatures** (stored apart from the message, e.g. for contracts) are allowed.
- **Confidentiality:** a random 128-bit **content-encryption key** is used once. Receiver decrypts the key with their private key, then the message.
- **Both:** the signature is made first, then message + signature are encrypted. Receiver decrypts, then verifies the signature.
- **Mandatory algorithms:** SHA-256, RSA with SHA-256, RSA key encryption, AES-128 CBC (others such as DSA, AES-256, 3DES are optional).
- **Certificates:** X.509 **v3**, signed by a CA. A user must register their key with a CA, and each client keeps a **local list of trusted keys and revocation lists** (local responsibility).

## 6. Other e-mail security services (syllabus terms)

| Service | Meaning |
|---|---|
| **Distribution lists** | Mail to many recipients; the list's keys and membership must be managed securely |
| **Establishing keys** | Setting up the keys that sign and encrypt mail |
| **Privacy / message-flow confidentiality** | Hiding message contents, and who talks to whom |
| **Source authentication** | Proof of who sent it |
| **Message integrity** | Proof it was not altered |
| **Non-repudiation** | Sender cannot deny sending it |
| **Proof of submission / delivery** | Evidence the mail was handed to the system / reached the recipient |
| **Anonymity** | Sending without revealing identity |

These are not detailed in the deck; the definitions are the standard meanings.

For PGP see [PGP](06-pgp.md).
