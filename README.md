# Network and Cyber Security — CS40017

Markdown notes, assignments and the lesson plan for **Network and Cyber Security**, 7th semester, School of Computer Engineering, KIIT.

The notes are distilled from the lecture decks: each concept is defined **once**, in the file where it fits best, and other files link to it rather than repeat it.

| | |
|---|---|
| **Subject code** | CS40017 |
| **Course teacher** | Dr. Nachiketa Tarasia |
| **Academic session** | Autumn Semester 2026 |
| **Contact hours** | 3 hours/week (LTP: 3-0-0) |

---

## Contents

| Path | What it is |
|---|---|
| [`notes/`](notes/) | The notes, one folder per module |
| [`assignments/`](assignments/) | Assignment answers |
| [`2025_midsem.md`](2025_midsem.md) | 2025 mid-semester paper with worked answers |
| [`2026_midsem.md`](2026_midsem.md) | 2026 mid-semester paper with worked answers |
| [`lesson-plan.md`](lesson-plan.md) | Full syllabus, course outcomes, books, evaluation scheme and activity schedule |

---

## Notes

### Module 1 — Introduction to Network Security *(Unit I)*

| # | Note | Covers |
|---|---|---|
| 1 | [Network Security Fundamentals](notes/module-1/01-network-security-fundamentals.md) | NIST definitions, CIA triad, authenticity & accountability, security challenges, network security model, network access security model |
| 2 | [OSI Security Architecture (X.800)](notes/module-1/02-osi-security-architecture-x800.md) | Attacks (passive/active), the five security services, specific & pervasive mechanisms, service–mechanism mapping |
| 3 | [Diffie–Hellman Key Exchange](notes/module-1/03-diffie-hellman-key-exchange.md) | Primitive roots, the algorithm, worked example (p=23, g=5), MITM limitation |
| 4 | [IPsec](notes/module-1/04-ipsec.md) | VPNs, IPsec standards, tunnel vs transport, SA & ISAKMP, AH vs ESP, packet formats, IKE phases, best practices |

### Module 2 — Cyber Security *(Unit II)*

| # | Note | Covers |
|---|---|---|
| 1 | [Introduction to Cyber Security](notes/module-2/01-cyber-security-introduction.md) | Definition, CIA pillars, seven types of cybersecurity, major threats, emerging trends, challenges |
| 2 | [Network Device Vulnerabilities](notes/module-2/02-network-device-vulnerabilities.md) | Eight vulnerability classes, each paired with its countermeasure |
| 3 | [Malware and Back Doors](notes/module-2/03-malware-and-backdoors.md) | Virus, worm, Trojan, bots/spyware/adware, back doors, social engineering, related terms |
| 4 | [DoS, Spoofing and Man-in-the-Middle](notes/module-2/04-dos-spoofing-and-mitm.md) | DoS/DDoS mechanics and history, IP spoofing, MITM/session hijacking |
| 5 | [Protocol, DNS, ARP and TCP/IP Attacks](notes/module-2/05-protocol-dns-arp-tcp-attacks.md) | Protocol DDoS (SYN/ICMP flood, Ping of Death, DNS amplification), DNS poisoning, ARP attacks, TCP/IP hijacking |
| 6 | [VLAN, DMZ and NAC](notes/module-2/06-vlan-dmz-and-nac.md) | VLANs vs subnets, DMZ single/dual firewall designs, NAC types and implementation |
| 7 | [Proxy Servers and Honeypots](notes/module-2/07-proxy-servers-and-honeypots.md) | Proxy operation and architecture, all proxy types in one table, honeypots and honeynets |
| 8 | [IDS and IPS](notes/module-2/08-ids-and-ips.md) | Intrusion detection components, misuse vs anomaly, NIDS/HIDS, IPS techniques and types, IDS vs IPS |
| 9 | [Protocol Analyzers and Content Filters](notes/module-2/09-protocol-analyzers-and-content-filters.md) | Packet sniffers, what they do, hardware vs software; content filtering types and benefits |

---

## Syllabus coverage

| Unit | Name | Lectures | Notes |
|---|---|---|---|
| **I** | Introduction to Network Security | 6 | ✅ Module 1 |
| **II** | Cyber Security | 12 | ✅ Module 2 |
| **III** | Cyber Law and Ethics | 4 | ❌ not yet written |
| **IV** | Authentication | 10 | ❌ not yet written |
| **V** | Firewalls and Web Security | 4 | ❌ not yet written |

> The mid-semester examination falls between Unit II and Unit III. See [`lesson-plan.md`](lesson-plan.md) for the full topic list under each unit.

**Two known gaps:**

1. **Media-Based Vulnerabilities** (Unit II) — the source deck covers the cyber security introduction but stops before this topic.
2. **Units III, IV and V in their entirety** — cyber law and ethics, authentication (Kerberos, X.509, PGP), and firewalls/web security.

Adding the corresponding decks lets these notes be extended in the same shape, as `notes/module-3/` and so on.

---

## About these notes

- **Deduplicated.** Where two decks covered the same ground — malware in both the Module 1 PPTX and Software Attacks Part 1, DoS/spoofing across both, the CIA triad in three places, repeated IPsec benefit slides, fourteen near-identical proxy-type slides — the material is stated once and cross-linked.
- **Diagrams** are [Mermaid](https://mermaid.js.org/) blocks, which render on GitHub, in VS Code and in Obsidian. Packet-layout figures are kept as fenced ASCII, since byte layouts read better that way.
- **Typos** carried in from the slides are corrected (`Excahnge` → Exchange, `Vulnerablities` → Vulnerabilities, `Spoofmg` → Spoofing); technical claims are left as the lecturer stated them.
- **Image-only slides were recovered, not skipped.** The scanned cyber security deck, the X.800 services ↔ mechanisms table, the dangerous-malware table, the TCP handshake figure, the NAC step diagram and the IDS topology were all extracted from the source files and transcribed.

### Source material

The notes are derived from one `.pptx` and six `.pdf` lecture decks, plus the `Lesson.docx` course handout. **Those files are not in this repository** — they are course material rather than my own work, and are excluded by [`.gitignore`](.gitignore). Each note names the deck and slide or page range it came from, so the two can be read side by side.
