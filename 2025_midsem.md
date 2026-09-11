## Q1 — One mark each

**a) Two consequences of ARP spoofing on a LAN**

1. **Man-in-the-middle** — traffic meant for the legitimate host is rerouted to the attacker, who can eavesdrop on or modify it.
2. **Denial of service** — the attacker drops the traffic instead of forwarding it, cutting the victim off. (Session hijacking is an equally valid second point.)

**b) Role of a security mechanism in the network security model**

A security mechanism is the process, or device implementing that process, designed to **prevent, detect, or recover from a security attack**. In the model it supplies the **security-related transformation** applied to the information (such as encryption or an appended code), which together with **secret information shared by the two principals** delivers the security service. Services are the goals; mechanisms are the machinery.

**c) Authentication vs authorization**

| Authentication | Authorization |
|---|---|
| Verifies **who you are** | Decides **what you are allowed to do** |
| Uses credentials, certificates, biometrics | Uses access control lists, roles, privileges |
| Happens **first** | Happens **after** authentication succeeds |

**d) Two main uses of a proxy server**

1. **Anonymity and security** — hides internal IP addresses and can block malicious sites, acting as an additional firewall layer.
2. **Caching and bandwidth optimisation** — stores frequently accessed content locally, reducing bandwidth use and latency. (Monitoring/logging user activity and content filtering are also acceptable.)

**e) What is a DMZ and why is it used**

A **demilitarised zone** is a perimeter network placed between the internet and the internal LAN, hosting external-facing services such as web, DNS, FTP and mail servers. It is used so that **untrusted internet traffic never touches the private network directly** — if an attacker compromises a DMZ server, an internal firewall still stands between them and internal data.

---

## Q2 — IPsec, AH vs ESP, and Diffie–Hellman [5]

**Working of IPsec**

IPsec secures IP data at the **network layer (Layer 3)**, so it is transparent to applications. It is a suite of four parts: **Security Associations**, **AH**, **ESP**, and **IKE**. Before traffic flows, IKE (UDP 500) negotiates a Security Association — the parameter set for the session, identified by SPI, destination IP, and protocol identifier. An SA is **unidirectional**, so two are needed for two-way traffic. AH or ESP then protects each packet, in either **transport mode** (IPsec header inserted into the existing packet) or **tunnel mode** (whole packet wrapped in a new IP header).

**AH vs ESP**

| | AH | ESP |
|---|---|---|
| IP protocol number | **51** | **50** |
| Authentication + integrity | Yes | Yes |
| **Confidentiality** | **No** | **Yes** (encryption) |
| Coverage | Entire packet, mutable IP fields zeroed | IPsec header + payload |

**Diffie–Hellman numerical** — p = 7, g = 5, a = 3, b = 4

```
Alice's public:  A = 5³ mod 7 = 125 mod 7 = 6
Bob's public:    B = 5⁴ mod 7 = 625 mod 7 = 2

Alice computes:  K = B^a mod 7 = 2³ mod 7 = 8 mod 7 = 1
Bob computes:    K = A^b mod 7 = 6⁴ mod 7 = 1296 mod 7 = 1
```

**Shared symmetric key = 1.** Both sides arrive at the same value without ever transmitting it.

---

## Q3 — Network Intrusion Detection System [5]

**What it is.** A NIDS monitors traffic at strategic points in a network, examining incoming and outgoing packets to detect unauthorised access attempts and issue real-time alerts.

**Architecture — three logical components**

| Component | Role |
|---|---|
| **Sensors** | Collect raw data — network packets, log files, system call traces — and forward it |
| **Analyzers** | Receive sensor input and determine whether an intrusion occurred, with evidence and suggested action |
| **User interface** | Lets the operator view output and control system behaviour (console/manager) |

**Placement.** The NIDS sits **out of band**, not in the live communication path. It taps a **SPAN or TAP port** to inspect a *copy* of the traffic, so it adds no latency to the network.

**How it detects malicious traffic — two approaches**

- **Misuse (signature) detection** — pattern-matches traffic against a database of known attack signatures. Accurate with few false alarms, but blind to novel attacks.
- **Anomaly detection** — compares activity against a baseline of normal behaviour. Catches unknown attacks, but carries a trade-off between false positives and false negatives.

It examines protocol and application-layer patterns and can flag DNS poisoning, malformed packets and port scans.

**Limitation.** A NIDS **detects but cannot prevent** — it raises alerts only. An IPS, placed inline, adds blocking.

---

## Q4 — DNS Spoofing vs DNS Poisoning [5]

**Differentiation**

| | DNS Spoofing | DNS Poisoning |
|---|---|---|
| Nature | The **act** — sending forged DNS responses to a query | The **resulting state** — falsified entries stored in the DNS cache |
| Duration | Momentary; affects that transaction | Persistent; served to every user until the entry expires |
| Scope | Usually one victim | All clients of the poisoned resolver |

They are often used interchangeably; the precise relationship is that **spoofing is the technique, poisoning is the corrupted cache it produces** — the same relationship as ARP spoofing to ARP poisoning.

**How the attack is carried out**

1. **Man-in-the-middle** — the attacker sits between browser and DNS server and corrupts the cached data.
2. **DNS server hijack** — the server itself is compromised and its configuration altered.
3. **Cache poisoning via spam** — poisoning code is embedded in emails using scare tactics to induce clicks.

**How communication security is compromised**

- **Confidentiality** — users are redirected to phishing sites and surrender credentials, which are stolen or resold.
- **Integrity** — the name-to-IP mapping the user relies on is falsified; the browser shows the correct domain while connecting to the attacker.
- **Availability** — spoofed security-update sites halt patching, leaving the victim permanently exploitable. Malware is also delivered via drive-by downloads.

**Prevention**

- **DNSSEC** — cryptographically signs DNS records so forged responses are rejected.
- **Close open resolvers** and restrict recursive queries to internal clients.
- **Regularly clear the DNS cache** and limit TTL values.
- **Use HTTPS with certificate validation**, so a redirected site fails the TLS check.
- **Deploy an IDS** to detect anomalous DNS traffic.

---

## Q5 — Denial of Service [5]

**What a DoS attack is.** An attack that overwhelms a target's ability to handle incoming communications. The attacker sends such a large number of connection or information requests that the system becomes overloaded and **cannot respond to legitimate requests**. It directly violates the **availability** leg of the CIA triad. Any internet-connected system offering TCP-based services — web, FTP or mail servers — is vulnerable.

**DoS vs DDoS**

| | DoS | DDoS |
|---|---|---|
| Source | A **single** machine | **Many** machines simultaneously |
| Preparation | None needed | A prep phase compromising thousands of hosts into **bots/zombies** |
| Traffic volume | Limited by one attacker's bandwidth | Massive and coordinated |
| Mitigation | Easy — block the offending IP | Hard — traffic comes from thousands of legitimate-looking addresses |
| Traceability | Attacker's IP is exposed | Real attacker hidden behind the botnet |

**Preventive mechanisms** (any two)

1. **Firewall rules with rate limiting** — cap the number of requests accepted per source per interval, dropping excess traffic before it reaches the server.
2. **IDS/IPS deployment** — an inline IPS detects flood patterns and automatically drops malicious packets, blocks the source address and resets connections.
3. **Ingress filtering at the router** — reject packets whose source addresses are forged, defeating spoofing-based amplification.
4. **Load balancing and traffic distribution** — spread requests across multiple servers so no single machine is exhausted.
