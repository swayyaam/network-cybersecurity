# Solutions — Autumn Mid Semester 2026, CS40017

## Q1 — One mark each

**a) Passive vs active attacks, with one example each**

| Passive | Active |
|---|---|
| Learns or uses information; **does not affect system resources** | **Modifies** a data stream or creates a false one |
| Eavesdropping, very hard to detect | Detectable, hard to prevent |
| Emphasis on **prevention** | Emphasis on **detection and recovery** |
| *Example:* traffic analysis | *Example:* masquerade |

**b) Purpose of a VLAN**

To logically group network resources on administratively defined switch ports, **breaking one large broadcast domain into smaller ones**. Each VLAN is a separate broadcast domain, which reduces broadcast traffic, improves performance, adds a layer of security by isolating groups, and allows grouping by function rather than physical location.

**c) ARP spoofing vs ARP poisoning**

| ARP Spoofing | ARP Poisoning |
|---|---|
| The **action** | The resulting **state** |
| Attacker sends fake ARP packets linking **his MAC** to a legitimate host's IP | The ARP tables of surrounding hosts now hold **falsified MAC maps** |
| A single forged broadcast | The corruption spreads across the LAN |

Spoofing is what the attacker does; poisoning is the corrupted cache it produces. Outcome: hijack, deny service, or sit in the middle.

**d) Deliberately vulnerable decoy system**

A **honeypot**. It mimics real, vulnerable services to attract attackers, holds no actual data, and records everything the attacker does while keeping them away from real systems. (Multiple connected honeypots form a **honeynet**.)

**e) Phishing vs spoofing**

- **Phishing** — using fake emails, messages or websites to trick users into revealing credentials or sensitive information. It targets the **human**.
- **Spoofing** — forging or modifying the **source IP address** so messages appear to come from a trusted host. It targets the **system's trust in addresses**.

**Difference:** phishing is social engineering that relies on deception of a person; spoofing is a technical forgery of identity at the packet level. Phishing needs the victim to act; spoofing does not.

---

## Q2a — IPsec recommendation for the VPN [2.5]

**Recommendation: ESP in Tunnel mode.**

**Why ESP and not AH**

The first requirement is **confidentiality of sensitive data**. AH (protocol 51) provides source authentication and data integrity but **no encryption whatsoever**, so it cannot meet this requirement at all. ESP (protocol 50) provides everything AH does **plus confidentiality** through symmetric encryption. It therefore satisfies both requirement 1 and requirement 2 — integrity and authentication — in a single protocol.

There is a second, decisive reason. Employees connect from behind **NAT devices**. AH authenticates the **entire packet including IP header fields**, so when a NAT device rewrites the source address, the authentication check fails and the packet is discarded. **AH is fundamentally incompatible with NAT.** ESP authenticates only the IPsec header and payload, leaving the outer IP header free to be translated, so it survives NAT traversal.

**Why Tunnel mode and not Transport mode**

Requirement 4 asks for a **secure tunnel between the remote device and the company firewall**, effectively extending the private network to the remote user. Tunnel mode encrypts the **entire original IP packet** and encapsulates it inside a **new IP header** addressed to the company firewall. This is what makes an endpoint-to-gateway VPN possible: the firewall is the visible destination, and it decapsulates and forwards the inner packet into the internal network.

Tunnel mode also **protects the original IP header**, hiding the internal addressing scheme from anyone observing the public path. Transport mode merely inserts an IPsec header into the existing packet, leaves the original header exposed, and is designed for direct host-to-host protection — not for extending a private network through a gateway.

**Requirement-by-requirement mapping**

| Requirement | Met by |
|---|---|
| Confidentiality of sensitive data | **ESP** — symmetric encryption of the payload |
| Integrity and authentication | **ESP** — also provides both, so no need to add AH |
| Works behind NAT | **ESP** — outer IP header not authenticated, so NAT rewriting does not break it |
| Secure tunnel to the company firewall | **Tunnel mode** — new IP header addressed to the gateway, original packet fully encapsulated |

---

## Q2b — DNS cache poisoning [2.5]

**What it is.** DNS poisoning occurs when **falsified data infiltrates a domain name server's cache**, causing DNS queries to return erroneous responses and redirect users to unintended websites. It is also called DNS spoofing or DNS cache poisoning. Since a DNS server catalogues domain names against their IP addresses, corrupting those records means the user types the correct domain but is silently sent to the attacker's server.

**How the attack is carried out — three routes**

1. **Man-in-the-middle** — the attacker interposes between the user's browser and the DNS server, then uses a specialised tool to corrupt the cached data on the device and manipulate the server's records, redirecting the user to a malicious site.
2. **DNS server hijack** — the attacker compromises the DNS server itself and manipulates its configuration. This is the most damaging form, because **every user** querying that server is rerouted to the fraudulent platform.
3. **Cache poisoning via spam** — poisoning code is embedded within emails, which typically use **scare tactics** to coerce the user into clicking a malicious link that initiates the exploit.

**The risks**

| Risk | Consequence |
|---|---|
| **Data theft** | User is redirected to a phishing site that harvests credentials, which are used or resold |
| **Malware infection** | Malicious sites infect machines via drive-by downloads and malicious links |
| **Halted security updates** | Attackers spoof update sites, so patches never arrive and the victim stays exploitable |
| **Censorship** | DNS manipulation restricts which sites can be reached |

**Preventive measures**

- **DNSSEC** — cryptographically signs DNS records so forged responses are rejected.
- **Close open resolvers** and restrict recursive queries to internal clients only.
- **Regularly clear the DNS cache** and keep TTL values short, limiting how long a poisoned entry survives.
- **Use HTTPS with certificate validation**, so a redirected site fails the TLS check even if resolution was poisoned.
- **Deploy an IDS** to detect anomalous DNS traffic and MITM activity.
- **User awareness** against clicking links in unsolicited email.

---

## Q3a — Establishing a shared secret over an insecure channel [2.5]

This is the **Diffie–Hellman key exchange**, conceived in **1976** and one of the first public-key protocols. It allows two parties to jointly establish a shared secret over an insecure channel **without ever transmitting the secret itself**. Note that it is **not an encryption algorithm** — it produces a key, which is then used with a symmetric cipher.

**Two defining properties**

1. The shared secret **cannot be computed by either party without the cooperation of the other**.
2. A third party observing **every** message transmitted during the exchange **cannot deduce the resulting secret**.

**The algorithm**

1. A and B publicly agree on a large prime **p** and a **primitive root g of p**. Both are public.
2. A selects a secret integer `a < p` and computes `A = g^a mod p`.
3. B selects a secret integer `b < p` and computes `B = g^b mod p`.
4. A sends `A` to B; B sends `B` to A.
5. A computes `K = B^a mod p`.
6. B computes `K = A^b mod p`.
7. Both now hold **`g^ab mod p`** — the shared symmetric key.

**Numerical example: p = 23, g = 5, a = 6, b = 15**

```
Alice's public value:  A = 5⁶  mod 23 = 15625 mod 23 = 8
Bob's public value:    B = 5¹⁵ mod 23              = 19

Alice sends 8 → ←  Bob sends 19

Alice computes:  K = B^a mod p = 19⁶  mod 23 = 2
Bob computes:    K = A^b mod p = 8¹⁵ mod 23 = 2
```

**Shared secret key = 2.** Both parties arrive at the same value, yet the number 2 was never transmitted.

| Step | Alice | Channel | Bob |
|---|---|---|---|
| Agree | p = 23, g = 5 | public | p = 23, g = 5 |
| Choose secret | a = 6 | — | b = 15 |
| Compute public | A = 8 | — | B = 19 |
| Exchange | send 8 → | | ← send 19 |
| Compute key | 19⁶ mod 23 = **2** | | 8¹⁵ mod 23 = **2** |

**Why the eavesdropper fails.** Eve sees `p`, `g`, `8` and `19` — every public value, which is by design. She still cannot compute the key because she knows neither `a` nor `b`, and recovering them from `g^a mod p` is the **discrete logarithm problem**, computationally infeasible for large `p`.

*(Note: a **primitive root** of prime n is a value `a` where `a^i mod n` yields every result from 1 to n−1 exactly once for i = 1…n−1. For n = 7 the primitive roots are 3 and 5.)*

---

## Q3b — Security limitations of Diffie–Hellman [2.5]

**The core limitation: DH is unauthenticated.** It establishes a shared key but **does nothing to verify who is on the other end**. The protocol guarantees that you share a secret with *someone* — not with the person you intended.

**Man-in-the-middle attack**

An attacker positioned between Alice and Bob runs **two separate exchanges**, one with each:

```
Alice  ←── key K1 ──→  Attacker  ←── key K2 ──→  Bob
```

1. Alice sends `g^a mod p`, intending it for Bob. The attacker intercepts it and sends Alice his own `g^m mod p` instead.
2. Bob sends `g^b mod p`. The attacker intercepts it and sends Bob `g^m mod p`.
3. Alice now shares key **K1 = g^am mod p** with the attacker, believing it is shared with Bob.
4. Bob shares key **K2 = g^bm mod p** with the attacker, believing it is shared with Alice.
5. The attacker decrypts everything from Alice with K1, reads or alters it, re-encrypts with K2, and forwards it to Bob — and vice versa. Neither party detects anything.

**Other limitations**

- **No authentication of the peer's identity** — the root cause of the above.
- **Small subgroup / weak parameter attacks** — if `g` is not a genuine primitive root or `p` is too small, the key space shrinks and brute force becomes feasible.
- **No confidentiality on its own** — DH only produces a key; without a cipher applied afterwards no data is protected.
- **No protection against replay** by itself.
- **Computationally expensive** modular exponentiation, which makes it a target for resource-exhaustion DoS.

**How to secure Diffie–Hellman**

- **Authenticated Diffie–Hellman** — run DH inside an authenticated exchange. Each party **signs** their public value with their private key, or authenticates using **digital certificates** from a trusted CA, so a substituted value is detected immediately.
- **Pre-shared secret** — both parties authenticate using a secret established out of band beforehand.
- **Station-to-Station protocol** — DH combined with mutual signature verification.
- **In practice, this is exactly what IKE Phase 1 does**: it performs a Diffie–Hellman exchange and then **authenticates the peer using certificates or a pre-shared secret**, which is what makes IPsec's key exchange safe against MITM.
- Use **large primes and verified primitive roots**, with standardised DH groups rather than ad-hoc parameters.

---

## Q4a — Security mechanisms across the layers [2.5]

Security can be implemented at any layer of the stack. The general trade-off: **the higher the layer, the more granular and end-to-end the protection, but the less transparent to applications; the lower the layer, the more transparent but the coarser the coverage.**

| Layer | Protocols | Security objective | Advantages | Limitations |
|---|---|---|---|---|
| **Application** | PGP, S/MIME, SSH, HTTPS | Confidentiality, integrity, authentication, **non-repudiation** for a specific application's data | True **end-to-end** protection; survives intermediate hops; fine-grained, per-message control | Must be **built into every application** separately; no protection for other traffic; key management burden on the app |
| **Transport** | SSL / TLS | Secure the TCP session — confidentiality, integrity, server (and optionally client) authentication | Protects all data of that session; widely deployed and mature; server authentication via certificates | Works only for **TCP**; applications must be **TLS-aware**; does not protect IP headers, so traffic analysis still possible |
| **Network** | **IPsec** (AH, ESP, IKE) | Confidentiality, integrity, source authentication, anti-replay for **all IP traffic** | **Transparent to applications** — no app changes needed; protects every protocol above it; supports VPNs via tunnel mode | Complex configuration and key management; AH breaks under NAT; protection ends at the tunnel endpoint, not the application |
| **Data link** | Link-layer encryption, WPA2/WPA3, MACsec | Protect data on a **single physical link** | Encrypts the **entire frame including headers**, defeating traffic analysis on that link; fast, often hardware-accelerated | **Hop-by-hop only** — data is decrypted at every intermediate node, so each node must be trusted; no end-to-end guarantee |

**The summary line to state:** application-layer security gives the strongest guarantees but must be rebuilt per application; network-layer security (IPsec) gives the best balance of transparency and coverage, which is why it underpins VPNs; link-layer security is fastest but offers no end-to-end assurance because every hop decrypts.

---

## Q4b — Denial of Service attacks [2.5]

**Definition.** A DoS attack attempts to **overwhelm a target's ability to handle incoming communications**, prohibiting legitimate users from accessing the system. The attacker sends such a large number of connection or information requests that the target becomes overloaded and cannot respond to genuine requests.

**DDoS.** A **distributed** denial of service launches a coordinated stream of requests from **many locations simultaneously**. It is usually preceded by a **preparation phase** in which thousands of systems are compromised and turned into **bots or zombies** directed remotely by the attacker.

**What is vulnerable.** Any system connected to the internet providing **TCP-based services** — web servers, FTP servers, mail servers — as well as routers and network servers running services such as echo.

**Major types**

| Type | Mechanism | Resource exhausted |
|---|---|---|
| **SYN flood** | Many SYN packets sent, handshake never completed; the server allocates resources and waits for connections that never establish | Server **memory and connection table** |
| **ICMP flood (ping flood)** | A mass of ICMP Echo Requests, each of which the system must generate a response to | **Bandwidth and processing** |
| **Ping of Death** | Malformed or **oversized ICMP packets** exceeding IP protocol limits; vulnerable systems crash, freeze or reboot on reassembly | **Crashes the reassembly logic** |
| **DNS amplification** | Spoofed queries for large records sent to **open resolvers**, which send enlarged responses to the victim whose IP was forged | **Victim's bandwidth** |
| **Volumetric / DDoS flood** | Sheer traffic volume from a botnet | Total available bandwidth |

**Protocol-based vs volumetric.** Protocol attacks target **the rules of how devices communicate** rather than raw bandwidth, sending fake, broken or incomplete packets that exploit connection handling at the lower layers — routers, firewalls, operating systems. They need **far fewer requests** than volumetric attacks to cause damage.

**Impact on network resources and services**

- **Availability is violated directly** — this is the CIA leg DoS attacks break by definition.
- **Server resources exhausted** — memory, CPU, and the connection table are consumed by illegitimate traffic.
- **Bandwidth saturated**, so legitimate packets are delayed or dropped.
- **Service downtime and lost income** — the British ISP CloudNine in January 2002 is believed to be the first business "hacked out of existence" by a DoS attack. Michael Calce's attacks on Amazon, CNN, eBay and Yahoo lasted around four hours and caused millions in lost revenue.
- **Damaged reputation** and loss of customer trust.
- **Cover for other attacks** — a DoS is used in TCP/IP hijacking to desynchronise the legitimate client.

**Preventive mechanisms.** Firewall rate limiting and SYN cookies; ingress filtering to drop spoofed source addresses; inline IPS to detect and drop flood traffic automatically; load balancing across multiple servers; closing open DNS resolvers; and traffic scrubbing services for large-scale DDoS.

---

## Q5a — IKE, and IKEv1 vs IKEv2 [2.5]

**What IKE does.** The Internet Key Exchange is the IPsec component used for **performing mutual authentication and establishing and maintaining Security Associations** (RFC 5996). It operates over **UDP port 500**. It automates what would otherwise be manual keying, and handles three jobs:

**1. Negotiation of security parameters.** The initiator sends one or more **proposals** — encryption algorithm, hash algorithm, authentication method, and DH group — and the responder selects one. This produces an agreed policy for the session.

**2. Establishment of shared secret keys.** IKE performs a **Diffie–Hellman exchange**, from which both peers independently derive the keying material. The secret is never transmitted.

**3. Peer authentication.** Because raw DH is vulnerable to MITM, IKE authenticates the peer using one of **three methods**: pre-shared key, public key encryption, or public key signature (certificates). This is what closes the MITM hole in plain DH.

**The two phases**

| Phase | Establishes | Using |
|---|---|---|
| **Phase 1** | A secure channel — the **ISAKMP SA** | Main mode or aggressive mode |
| **Phase 2** | The channel for actual data — the **IPsec SAs** | Quick mode |

Phase 1 **main mode** uses **six messages** in three steps: negotiate IKE policy, perform the authenticated DH exchange, then exchange identity and authentication material under encryption. **Aggressive mode** achieves the same in **three messages** but offers **no identity protection and no DoS protection**. Phase 2 **quick mode** runs entirely inside the protection of the ISAKMP SA and yields **two IPsec SAs — one inbound, one outbound**.

**IKEv1 vs IKEv2**

| | **IKEv1** | **IKEv2** (RFC 5996, 2010) |
|---|---|---|
| **Messages to establish** | 9 in main mode (6 + 3), or 6 in aggressive mode | **4 messages** total |
| **Modes** | Main, aggressive, quick — separate negotiation modes | **No mode distinction** — a single simplified exchange |
| **NAT traversal** | Add-on extension, not native | **Built in** |
| **Dead Peer Detection** | Optional extension | **Built in** (liveness checks) |
| **Authentication** | Both peers must use the **same** method | Peers may use **different** methods; supports **EAP** for remote access |
| **Reliability** | No built-in reliability; unacknowledged messages | **Request/response with sequence numbers**, so every message is acknowledged |
| **DoS resistance** | Vulnerable to half-open SA exhaustion | **Anti-DoS cookie** mechanism before state is committed |
| **MOBIKE (mobility/multihoming)** | Not supported | **Supported** — connection survives an IP address change |
| **Complexity / overhead** | High | Lower latency, less bandwidth, simpler implementation |

**The one-line difference to state:** IKEv2 does the same job as IKEv1 in **fewer than half the messages**, with NAT traversal, dead peer detection, EAP support and DoS protection built into the protocol rather than bolted on as extensions.

---

## Q5b — Demilitarized Zone [2.5]

**What it is.** A DMZ is a **perimeter network** that protects and adds an extra layer of security to an organisation's internal LAN from untrusted traffic. Its goal is to let the organisation **access and be accessed from untrusted networks such as the internet while keeping the private network secure**.

**What it holds.** External-facing services and resources: DNS servers, FTP servers, mail servers, proxy servers, VoIP servers and web servers.

**Why it exists.** A business with a public website needs internet-accessible servers. Placing those servers on the internal LAN would expose internal data to anyone who compromised them. The DMZ **isolates the servers from the LAN**, so a breach of a public server does not translate into a breach of internal data. It minimises LAN vulnerabilities while still permitting qualified internet traffic to reach the services that need it.

**How it works**

- The DMZ **buffers the internet from the private network**. It is isolated by a **security gateway, typically a firewall**, which filters traffic between the DMZ and the LAN. A second gateway filters external traffic before it reaches the DMZ servers.
- Incoming packets are therefore **screened by a firewall before reaching any DMZ-hosted server**.
- **Defence in depth:** if an attacker breaches the external firewall and compromises a DMZ system, they **still have to pass an internal firewall** to reach sensitive corporate data. A skilled attacker may breach the DMZ, but the resources inside it **should trigger alarms**, giving warning of the ongoing breach.

**Design one — single firewall**

Requires **three or more network interfaces**: one to the external public internet connection, one to the internal network, and one to the DMZ. Rules on the firewall monitor and control which traffic may reach the DMZ and strictly limit connectivity from the DMZ into the internal network.

```
Internet ──→ [ Firewall (3+ interfaces) ] ──→ Internal LAN
                        │
                        └──→ DMZ (web, DNS, FTP, mail, proxy)
```

**Design two — dual firewall** *(generally the more secure option)*

Two firewalls are deployed with the DMZ between them. The **first firewall admits external traffic only into the DMZ**. The **second admits only traffic going from the DMZ into the internal network**. An attacker would have to **compromise both firewalls** to reach the LAN — and using firewalls from two different vendors makes that harder still.

```
Internet ──→ [ Firewall 1 ] ──→ DMZ ──→ [ Firewall 2 ] ──→ Internal LAN
             external traffic            only DMZ traffic
             into DMZ only               into LAN
```

**The key point to state:** the DMZ works because it assumes public-facing servers **will eventually be compromised**, and positions them so that the compromise is contained rather than fatal.
