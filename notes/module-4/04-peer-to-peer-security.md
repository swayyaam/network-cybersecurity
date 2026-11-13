# Security in Peer-to-Peer Connections

> **Source:** `Module 4/4.5 Security in peer-to-peer connections.pptx`, slides 2–7 · **Syllabus:** Unit IV

## Key points

- In a **P2P network** every node is an equal **client and server**; there is no central server.
- **Strengths:** scalability and resilience. **Weakness:** security is **distributed**, so each node must protect itself.
- Main attack named: **man-in-the-middle**. Defense: **authentication + end-to-end encryption**.

---

## 1. P2P vs client–server

| | Client–server | Peer-to-peer |
|---|---|---|
| Structure | One powerful server serves many weaker clients | All nodes equal; each shares files, bandwidth and computing power |
| Failure | Server is a single point of failure | A node leaving is fine; traffic reroutes |
| Control | Central | None central |

The idea predates the internet (ARPANET, 1960s).

## 2. Types

| Type | Idea | Examples |
|---|---|---|
| **Unstructured** | No organizing structure → decentralized, private, easy to manage | Napster (1999), Gnutella (2000), BitTorrent (2004), Kazaa |
| **Structured** | Built for **efficient search**, using a **distributed hash table (DHT)** | CAN, Chord, Pastry, Tapestry |

## 3. Pros and cons

| Advantages | Disadvantages |
|---|---|
| **Scalable**: more nodes add capacity, no bottleneck | **No central security or data control**; more open to intrusion |
| **Self-healing**: works despite failures | Easy spread of **malware and unauthorized content** |
| | Resources may be used **inefficiently** |

## 4. Securing P2P

- **Per-device protection**: up-to-date antivirus, firewalls, encryption for data in transit.
- **User awareness**: understand file-sharing risks; download only from **trusted sources**.
- **Against man-in-the-middle** (attacker intercepts and alters data between two nodes): use P2P systems with **authentication** and **end-to-end encryption**.

**Uses:** file sharing (BitTorrent), distributed computing (SETI@home), cryptocurrency (Bitcoin).
