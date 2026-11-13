# Encrypted Tunnels and Cookies

> **Source:** `Module 5/5 Firewalls  Web Security.pdf`, pages 9–21 · **Syllabus:** Unit V

## Key points

- An **encrypted tunnel** is a secure pathway between two systems; only the intended recipient (with the key) can read the data. Types: **VPN, SSH tunnel, SSL/TLS**.
- Costs: **slower** connections and **trust in the provider**.
- A **cookie** is a small text file a server gives a browser to track sessions and users; it holds **no code** and cannot read the disk or carry viruses.
- Cookies are classed as **session vs persistent** and **first-party vs third-party**, plus **secure** cookies.

---

## 1. Encrypted tunnels

Data inside the tunnel is encrypted, like information locked in a box that only the key holder can open.

**Why:** protect private information from hackers; keep passwords, messages and card details safe; prevent data being changed or stolen in transit.

| Type | Use |
|---|---|
| **VPN** (Virtual Private Network) | Secures your whole internet connection |
| **SSH tunnel** | Secure access to remote computers |
| **SSL/TLS** | Secure websites (`https://`) |

**Uses:** safe browsing on public Wi-Fi, reaching work files from home, online banking and shopping, private email and messages.

**Limitations:** can **slow the connection**; you must **trust the VPN or service provider**.

(IPsec, covered in [Module 1](../module-1/04-ipsec.md), is the network-layer tunnel and VPN standard.)

## 2. Cookies

An **HTTP cookie** is a piece of data a server sends to the browser. The browser stores it and **returns it with every request** to that site, keeping sessions continuous. The name comes from UNIX "magic cookies".

- Plain text, **no executable code**; cannot access your drive or spread viruses.
- It stores only what you gave the site (including personal or financial data if you entered it), so **avoid giving credentials to untrusted sites**.

**Uses:**

| Use | What it does |
|---|---|
| **Session management** | Remembers cart, language, login state |
| **Tracking** | Advertisers and shops monitor browsing for targeted ads and recommendations |
| **Personalization** | Shows content matching past interests |
| **Staying logged in** | Avoids re-entering credentials |

**Storage:** as small text files on the device, in the browser's own data store (Chrome: a `Cookies` file in the profile folder; Firefox: `cookies.sqlite`); viewable and manageable in browser privacy settings.

**Parameters:**

| Parameter | Meaning |
|---|---|
| **Name**, **Value** | The cookie's name and data |
| **Expiration date** | How long it stays active |
| **Path** | URL path where it is valid |
| **Domain** | Extends validity to all servers in the domain |
| **Secure** | Only sent over a secure connection (SSL) |

**Types:**

| Type | Description |
|---|---|
| **Session** | Temporary; **deleted when the browser closes** |
| **Persistent** | Stays for a set time or until deleted; remembers preferences and logins |
| **First-party** | Set by the site you are visiting; for login, preferences, cart |
| **Third-party** | Set by **another site, usually advertisers**; tracks you across sites for ads, analytics, social plugins |
| **Secure** | Sent **only over HTTPS**, protecting data from interception |
