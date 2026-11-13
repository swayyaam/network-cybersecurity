# Pretty Good Privacy (PGP)

> **Source:** `Module 4/4.6 E-Mail Security.pptx`, slides 29–38 · **Syllabus:** Unit IV

## Key points

- **PGP** is free, open-source e-mail security software.
- Five services: **authentication, confidentiality, compression, e-mail compatibility, segmentation**.
- **Authentication** = hash (SHA-1) + RSA signature. **Confidentiality** = one-time symmetric key, itself encrypted with the receiver's public key.
- Algorithms: **RSA, DSS, Diffie–Hellman** (public key); **CAST-128, IDEA, 3DES** (symmetric); **SHA-1** (hash).

---

## 1. Why PGP is popular

- Free worldwide, on many platforms; commercial versions offer support.
- Includes strong, standard algorithms (above).
- Works for both **files and messages**, with anyone on the internet.

## 2. Notation

| Symbol | Meaning |
|---|---|
| **Ks** | one-time session key (symmetric) |
| **PRa / PUa** | private / public key of user A |
| **EP / DP** | public-key encryption / decryption |
| **EC / DC** | symmetric encryption / decryption |
| **H** | hash function |
| **‖** | concatenation |
| **Z** | ZIP compression |
| **R64** | conversion to radix-64 ASCII |

## 3. Authentication (digital signature)

1. Sender writes the message.
2. **SHA-1** produces a **160-bit hash**.
3. The hash is **encrypted with RSA using the sender's private key** and **prepended** to the message.
4. Receiver decrypts the hash with the **sender's public key**.
5. Receiver hashes the message again and compares; a match means **authentic**.

RSA means only the private-key owner can sign; SHA-1 means nobody can make a different message with the same hash. **DSS/SHA-1** is an alternative, and **detached signatures** (stored or sent separately) are supported.

## 4. Confidentiality

Only sender and receiver can read the message:

1. Message is compressed (Z) and encrypted with a one-time session key **Ks** (symmetric: CAST-128, IDEA or 3DES).
2. **Ks is encrypted with the receiver's public key** (EP, PUb) and attached.
3. Receiver recovers Ks with their **private key** (DP, PRb), decrypts the message, then decompresses.

## 5. Other services

| Service | Purpose |
|---|---|
| **Compression** | ZIP shrinks the message for storage and transmission (applied after signing, before encryption) |
| **E-mail compatibility** | **Radix-64** turns binary output into ASCII so mail systems accept it |
| **Segmentation** | Splits messages that are too long for mail size limits (standard PGP behaviour; not detailed in the slides) |
