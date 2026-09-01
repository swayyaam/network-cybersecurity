## Assignment 1

**1. The CIA triad**

The CIA triad is the set of three fundamental security objectives that NIST (FIPS 199) defines for information and information systems.

- **Confidentiality** — private or confidential information is not disclosed to unauthorized individuals. It has two sides: _data confidentiality_ (protecting the data itself) and _privacy_ (individuals control how, when and to what extent their information is shared).
- **Integrity** — _data integrity_ means information and programs are changed only in a specified and authorized manner; _system integrity_ means the system performs its intended function unimpaired, free from unauthorized manipulation.
- **Availability** — systems work promptly and service is not denied to authorized users.

They are the core objectives because every security mechanism ultimately exists to preserve one or more of them: encryption serves confidentiality, hashes and checksums serve integrity, and redundancy and DoS defences serve availability. Two further requirements are usually added alongside them — **authenticity** (being genuine and verifiable) and **accountability** (actions traceable uniquely to an entity, enabling forensic analysis).

**2. Passive vs. active attacks**

||Passive|Active|
|---|---|---|
|Goal|Learn or make use of information|Modify a data stream or create a false one|
|Effect on resources|None|Alters data or system state|
|Nature|Eavesdropping, monitoring|Interference|
|Examples|Release of message contents, traffic analysis|Masquerade, replay, modification of messages, DoS|
|Countermeasure emphasis|**Prevention**|**Detection and recovery**|

A passive attack only observes — the intruder listens and alters nothing, so no message is delayed, corrupted or duplicated and neither sender nor receiver sees any symptom. There is nothing anomalous in the traffic to trigger an alarm, and traffic analysis works even against encrypted data because the attacker studies only the frequency and length of messages, not their content. Since detection is essentially impossible, the emphasis is on prevention through encryption and traffic padding. Active attacks are the reverse: they leave evidence in the data stream and so are detectable, but cannot be absolutely prevented.

**3. Virus vs. worm propagation**

A **virus** is not self-sufficient. It consists of code segments that attach themselves to an existing host program and become part of it, much like a biological pathogen using a cell's replication machinery. Crucially, _the virus may exist on a system but stays inactive and unable to spread until a user or the operating system runs or opens the infected host file_ — the classic route being an e-mail attachment. Propagation is therefore host-dependent and user-triggered.

A **worm** is standalone. It replicates fully functional copies of itself without needing a host program, and its behaviour can be initiated with or without the user downloading or executing anything — this is the key difference. It spreads either by exploiting a vulnerability on the target system or by using social engineering to trick users into running it. Once resident, it can mail itself to every address found on the machine and drop copies onto every reachable web server. Because it replicates autonomously, it keeps going until it exhausts memory, disk space and network bandwidth.

---

## Assignment 2

**1. AH vs. ESP**

||AH|ESP|
|---|---|---|
|IP protocol number|51|50|
|Source authentication|✓|✓|
|Data integrity|✓|✓|
|Confidentiality|✗|✓|
|Coverage|Entire packet, mutable IP header fields zeroed out|IPsec header + payload|

Both protocols "sign" the packet with a keyed hash based on a shared secret, which detects modification and, because only the peers hold the secret, also authenticates the source. The difference is that **ESP additionally applies symmetric-key encryption to the payload**, so it protects against disclosure as well as spoofing and replay. AH deliberately carries no encryption: its design goal is to authenticate as much of the packet as possible, including IP header fields that ESP leaves outside its protection. The cost is that AH cannot cover mutable fields (ToS, TTL, Header Checksum, Offset, Flags), which change legitimately in transit and are zeroed for the calculation. Within ESP, encryption occurs before authentication; if both protocols are applied to one packet, AH follows ESP. Best practice is ESP, since it gives integrity _and_ confidentiality.

**2. Transport mode vs. tunnel mode**

```
Original:   [ IP Header ][ TCP Header ][ Payload ]
Transport:  [ IP Header ][ IPsec Header ][ TCP Header ][ Payload ]
Tunnel:     [ New IP Header ][ IPsec Header ][ IP Header ][ TCP Header ][ Payload ]
```

**Transport mode** simply inserts the IPsec header into the existing packet. No new packet is created, the original IP header stays in place and visible, and the size increase is minimal — useful where a larger packet would cause fragmentation problems. Only the upper-layer data is protected. It is the usual choice for remote-access VPNs and end-to-end host communication.

**Tunnel mode** encrypts the entire original IP packet and makes it the payload of a new, larger IP packet with a new IP header. Because the original header is itself protected, the internal addressing of the site is hidden from anyone observing the tunnel — the outside world sees only traffic between the two gateways. This is what makes tunnel mode the standard for site-to-site (gateway-to-gateway) VPNs, at the cost of the extra header overhead.

**3. The two phases of IKE**

IKE runs over UDP port 500 and performs mutual authentication and the establishment of Security Associations. It works in two phases because it must first build a protected channel before it can safely negotiate anything else.

**Phase 1** establishes the **ISAKMP SA** — a bidirectional secure channel between the two peers. It runs in _main mode_ (six messages, three exchanges) or _aggressive mode_ (three messages, faster but with no identity protection and no DoS protection). Main mode has three steps: negotiate the IKE policy (encryption algorithm, hash algorithm, authentication method, DH group); perform a **Diffie–Hellman exchange** to derive keying material; then exchange authentication material and authenticate the peer using certificates or a pre-shared secret. The DH exchange alone is unauthenticated and vulnerable to MITM — step 3 is what closes that hole.

**Phase 2** runs _quick mode_ entirely **inside the protection of the ISAKMP SA**, so all its traffic is encrypted. It negotiates the parameters for the actual IPsec session — which protocol (AH or ESP), which algorithms, which traffic to protect — and creates or refreshes the keys. Because an SA is unidirectional, each quick mode negotiation produces **two IPsec SAs, one inbound and one outbound**.

---

## Assignment 3

**1. Primary function of Diffie–Hellman**

Diffie–Hellman, conceived in 1976 as one of the first public-key protocols, is a method for two parties to **jointly establish a shared secret key over an insecure public channel**. It is _not_ an encryption algorithm — it produces a key, which is then used with a symmetric cipher to encrypt the actual data.

The key itself is never transmitted. Both sides publicly agree on a large prime `p` and a primitive root `g` of `p`. Each then chooses a private exponent and sends only `g^x mod p` across the channel. Each combines their own secret with the other's public value and arrives independently at the same number `g^ab mod p`. Two properties follow: the shared secret cannot be computed by either party without the other's cooperation, and a third party who observes every message of the exchange still cannot deduce it.

**2. Numerical calculation (p = 23, g = 5, a = 6, b = 15)**

_Public keys:_

- A computes `A = g^a mod p = 5⁶ mod 23 = 15625 mod 23 = **8**`
- B computes `B = g^b mod p = 5¹⁵ mod 23 = **19**`

A transmits 8 to B; B transmits 19 to A.

_Shared secret:_

- A computes `K = B^a mod p = 19⁶ mod 23 = **2**`
- B computes `K = A^b mod p = 8¹⁵ mod 23 = **2**`

**Shared secret key = 2.** Both sides agree because `(g^b)^a ≡ (g^a)^b ≡ g^ab mod p`.

| Step     | Alice          | Channel | Bob             |
| -------- | -------------- | ------- | --------------- |
| Agree    | p = 23, g = 5  | → ←     | p = 23, g = 5   |
| Secret   | a = 6          |         | b = 15          |
| Public   | 5⁶ mod 23 = 8  |         | 5¹⁵ mod 23 = 19 |
| Exchange | send 8 →       |         | ← send 19       |
| Key      | 19⁶ mod 23 = 2 |         | 8¹⁵ mod 23 = 2  |

**3. Why an eavesdropper cannot deduce the key**

An observer, Eve, learns four values: `p = 23`, `g = 5`, `A = 8` and `B = 19`. What she does not learn is either private exponent, `a` or `b`, and without one of them she cannot perform the final exponentiation — she has no way to combine 8 and 19 into 2.

To recover `a` from `A = g^a mod p` she would have to solve the **discrete logarithm problem**: find the exponent `a` such that `5^a ≡ 8 (mod 23)`. Modular exponentiation is cheap to compute in the forward direction but, for a large prime, computationally infeasible to reverse — this asymmetry is the security foundation of the protocol. With `p = 23` the search space is trivial and Eve could brute-force it, which is why real deployments use primes of 2048 bits or more.

One caveat worth stating: this argument holds against a **passive** eavesdropper only. Plain DH does not authenticate who is at the other end, so an active man-in-the-middle can run a separate exchange with each party and relay traffic between them. That is why real protocols run DH inside an authenticated exchange, such as IKE Phase 1.