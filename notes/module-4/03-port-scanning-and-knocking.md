# Port Scanning and Port Knocking

> **Source:** `Module 4/4.3 Authentication_Port Scanning.pptx`, slides 2–9; `4.4 Port Knocking.pptx`, slides 2–7 · **Syllabus:** Unit IV

## Key points

- A **port scan** sends packets to ports and reads the replies to find open ports, running services and firewalls. Attackers and defenders both use it.
- Ports are numbered **0–65,535**: well-known (0–1,023), registered (1,024–49,151), dynamic/private (49,152–65,535).
- Scan types differ in **accuracy vs. stealth**: full connect is accurate but logged; SYN, FIN/Xmas and FTP bounce try to avoid logs.
- **Port knocking** hides services: all ports stay closed until the client sends the **secret sequence** of connection attempts.
- Its weakness is that the sequence can be **sniffed and replayed**.

---

## 1. Ports

A **port** is a numbered endpoint where programs exchange data; IP address + port number identifies a service. Ports are managed over **TCP** (reliable connections) or **UDP** (low-latency, loss-tolerant).

| Range | Name | Note |
|---|---|---|
| 0–1,023 | **Well-known** | Assigned by IANA |
| 1,024–49,151 | **Registered** | Registered by software companies |
| 49,152–65,535 | **Dynamic / private** | Free for anyone |

Common ports: **20** FTP data · **22** SSH · **23** Telnet (unencrypted) · **53** DNS · **80** HTTP.

## 2. Port scanning

A scan reveals the **services running, who owns them, whether anonymous login is allowed, and which services need authentication**. Defenders use tools such as **Nmap**, **Netcat** and IP scanners to find their own weak points.

| Technique | How it works | Stealth |
|---|---|---|
| **Vanilla (full connect)** | Tries all 65,536 ports with a full **SYN → SYN-ACK → ACK** handshake | Accurate but **always logged** by firewalls |
| **SYN (half-open)** | Sends SYN, reads SYN-ACK, **never completes** the handshake | Usually **not logged** |
| **FIN scan** | Sends an unsolicited FIN. **Closed port replies RST; open port ignores it** | Avoids logs |
| **Xmas scan** | Sends a packet with **all flags set**; the reply reveals port/firewall state | Avoids logs |
| **FTP bounce** | Bounces packets through an FTP server to **hide the scanner's location** | Anonymous |
| **Sweep** | Pings the **same port across many hosts** to find which are alive (no port-state info) | Used as a first step |

An online port checker reports a port as **open** (accepting requests) or **timed out** (blocked or unavailable).

**Prevention:**

- **Strong firewall**: controls port visibility and can detect and stop a scan in progress.
- **TCP wrappers**: allow or deny access by IP address or domain name.
- **Regular self-scans**: find ports that are open but not needed.

## 3. Port knocking

A technique that keeps **every port closed** (since only open ports are a risk) and opens the service port only after the client **"knocks" a predefined sequence of connection attempts** on closed ports.

*Example:* the server does not listen on port 22 until it sees attempts to ports **1024, 3302, 31337 in order within 5 seconds**; it then listens on 22 for an SSH connection for **10 seconds**.

**Tools:**

| Tool | Notes |
|---|---|
| **knockd** | Open-source daemon; watches for the knock sequence and opens the port |
| **fwknop** (FireWall KNock OPerator) | Needs the knock **and** a valid **encrypted Single Packet Authorization (SPA)** packet |
| **pyknock** | Python implementation; custom sequences, TCP and UDP |

**Useful for:** unpatched **zero-day** flaws in exposed services, **legacy/end-of-life** systems, and **highly sensitive** services reachable only by authorized systems.

| Advantages | Disadvantages |
|---|---|
| Ports hidden by default → **smaller attack surface**, scans find nothing | A **sniffed** sequence can be **replayed** by an attacker |
| **Simple**: minimal firewall/server changes, no VPN needed | Users must perform the exact sequence, which is harder to use |
| Lightweight pre-authentication that complements other controls | **Packet loss** can drop knocks and block legitimate users |
| Blocks unauthorized users from even seeing service ports | Closed ports make **monitoring and troubleshooting** harder |
