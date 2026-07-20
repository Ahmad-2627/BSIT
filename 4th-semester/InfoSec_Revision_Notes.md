# 📘 INFORMATION SECURITY — COMPLETE REVISION NOTES
### Ahmad's Full Course Notes (All 17 Phases)

---

# PHASE 1: FOUNDATIONS

## Data vs Information
- **Data** = raw facts, no meaning (e.g., `92, 85, 78`)
- **Information** = data + context/meaning (e.g., "Ahmad scored 92 in DB")

```
Data (raw numbers) → (processing/context) → Information (meaningful)
```

## Information Security
Protecting information from unauthorized **access**, **modification**, and **destruction**.

## Asset → Threat → Vulnerability → Risk

```
ASSET (valuable thing)
   ↑ needs protection from
THREAT (possible danger)
   ↓ which exploits
VULNERABILITY (a weakness)
   ↓ resulting in
RISK (chance damage actually happens)
```

- **Asset** = valuable thing (data, laptop, bank account)
- **Threat** = possible danger (hacker, virus, fire)
- **Vulnerability** = weakness (weak password, outdated software)
- **Risk** = Threat × Vulnerability × Impact

## CIA Triad

```
                 Confidentiality
                       /\
                      /  \
                     / CIA \
                    /  Triad \
                   /__________\
    Integrity                  Availability
```

| Goal | Meaning | Tool |
|---|---|---|
| Confidentiality | Only authorized see it | Encryption |
| Integrity | Data stays accurate | Hashing, Digital Signatures |
| Availability | Accessible when needed | Backups, Redundancy |

## Security Services vs Security Mechanisms
- **Service** = the GOAL (Confidentiality, Authentication, Non-Repudiation)
- **Mechanism** = the TOOL (Encryption, Digital Signature, Firewall)

```
Security SERVICE (the goal)  →  Security MECHANISM (the tool)
```

## Pervasive vs Specific Mechanisms
- **Specific** = tied to ONE service (Encryption → Confidentiality only)
- **Pervasive** = system-wide, general (Audit trails, Security labels, Event detection, Security recovery)

## Threat vs Attack
- **Threat** = potential danger (hasn't happened)
- **Attack** = actual attempt to exploit

## Types of Attacks

```
                    ATTACKS
                   /        \
            PASSIVE          ACTIVE
        (only watch)    (actually DO something)
     Violates:              Violates:
     Confidentiality         Integrity + Availability
     Hard to detect ❌       Easier to detect ✅
```

### Passive Attacks
- **Eavesdropping** — secretly listening
- **Traffic Analysis** — studying communication patterns (even if encrypted)
- **Sniffing** — capturing network packets (e.g., Wireshark on public WiFi)

### Active Attacks
- **Masquerade** — pretending to be someone else
- **Replay** — capturing & resending legitimate data later
- **MITM (Man-in-the-Middle)** — attacker sits between two parties (violates Confidentiality AND Integrity)
- **DoS/DDoS** — flooding to crash a system
  - DoS = 1 attacking machine (easy to block — 1 IP)
  - DDoS = thousands of machines/botnet (very hard to block)
- **Modification** — changing data in transit
- **Fabrication** — creating fake data (phishing)
- **Interruption** — destroying/blocking availability
- **Interception** — unauthorized access (confidentiality)

### Attack vs CIA Mapping

| Attack | CIA Violated |
|---|---|
| Eavesdropping / Traffic Analysis / Sniffing | Confidentiality |
| Modification / Fabrication / Replay / Masquerade | Integrity |
| DoS / DDoS / Interruption | Availability |
| MITM | Confidentiality + Integrity |

---

# PHASE 2: SECURITY DESIGN PRINCIPLES

| Principle | Core Idea | Example |
|---|---|---|
| Least Privilege | Give minimum access needed | Cashier can't change interest rates |
| Defense in Depth | Multiple security layers | Firewall + Antivirus + Encryption |
| Separation of Duties | Split tasks between people | One approves, another processes payment |
| Fail-Safe Defaults | Deny by default | New employee starts with zero access |
| Economy of Mechanism | Keep design simple | Simple login = fewer bugs |
| Complete Mediation | Check EVERY access, EVERY time | Re-verify permission on every file open |
| Open Design | Security = secret KEY, not secret algorithm | AES is public, only key is secret |
| Least Common Mechanism | Avoid shared components | Separate accounts, not shared logins |
| Psychological Acceptability | Must be easy to use | Overly complex password → sticky notes |

```
Defense in Depth Visual:
Attacker → [Firewall] → [Antivirus] → [Encryption] → [Access Control]
           blocked?      blocked?      unreadable?    still needs login?
```

---

# PHASE 3: CRYPTOGRAPHY FOUNDATIONS

## Why Cryptography Exists
To protect information while stored/sent, especially over unsafe channels.

## Core Terms

```
Plaintext ("HELLO")
      ↓
  ENCRYPTION  ← uses KEY
      ↓
Ciphertext ("KHOOR")
      ↓ (sent over network)
  DECRYPTION  ← uses correct KEY
      ↓
Plaintext ("HELLO")
```

| Term | Meaning |
|---|---|
| Plaintext | Original readable message |
| Ciphertext | Scrambled, unreadable message |
| Encryption | Plaintext → Ciphertext |
| Decryption | Ciphertext → Plaintext |
| Key | Secret value controlling the process |

## 4 Goals of Cryptography
1. **Confidentiality** — keeps message secret
2. **Integrity** — proves message wasn't changed
3. **Authentication** — proves WHO sent it
4. **Non-Repudiation** — sender can't deny sending it

## Frequency Analysis
Compares how often letters appear in ciphertext vs known English frequency (E, T, A, O most common; Z, Q, X rare).

- Works well on: Caesar, Monoalphabetic, Playfair
- Doesn't work on: Vigenère (until Kasiski finds key length), Vernam, OTP
- Longer ciphertext = easier to break this way

---

# PHASE 4: CLASSICAL CIPHERS

## 1. Caesar Cipher
Shift every letter by fixed key.
```
Formula: C = (P+K) mod 26      Decrypt: P = (C-K) mod 26
```
Weakness: only 25 keys → brute force in seconds.

## 2. Monoalphabetic Cipher
Random full substitution table (26! possible keys ≈ 4×10²⁶).
Weakness: single-letter frequency analysis.

## 3. Playfair Cipher
Encrypts LETTER PAIRS using a 5×5 grid (I/J share a cell).

```
Grid rules:
Same ROW      → shift each letter RIGHT (wrap if needed)
Same COLUMN   → shift each letter DOWN (wrap if needed)
Rectangle     → swap to own row, other's column
```

**Edge Cases:**
- Repeated letters in a pair → insert filler X, re-split
- Odd number of letters → add filler X at end
- Numbers/symbols → CANNOT be encrypted directly (25-cell grid = letters only)
- Case-insensitive; I and J treated as same letter

Weakness: digraph (pair) frequency analysis.

## 4. Hill Cipher
Matrix multiplication: `C = (Key Matrix × Plaintext Vector) mod 26`

```
| a  b |   | x |     | ax+by |
| c  d | × | y |  =  | cx+dy |   (then mod 26)
```

Decryption needs the **inverse key matrix** (determinant + modular inverse mod 26).
Weakness: known-plaintext attack (algebra solves for the key).

## 5. Rail Fence Cipher
**Transposition** cipher — rearranges letters in a zigzag across "rails" (rows), key = number of rails.
```
D . . . N . . . E
. E . E . D . H .
. . F . . . T . .
```
Read row by row. Weakness: very small key space (few realistic rail counts).

## 6. Row (Columnar) Transposition
Key = keyword → sets column order (alphabetical). Fill grid row-wise, read COLUMN by column (in keyword's alphabetical order).
Weakness: anagramming; letter frequency still visible (proves transposition, not substitution).

## 7. Double Columnar Transposition
Apply Columnar Transposition TWICE with 2 keywords.
```
Plaintext → [Transposition w/ Keyword1] → Intermediate → [Transposition w/ Keyword2] → Final Ciphertext
```
Decrypt: reverse in OPPOSITE order (undo Keyword2 first, then Keyword1).

## 8. Vigenère Cipher
Repeating KEYWORD shifts each letter differently (**Polyalphabetic**).
```
Plaintext:  H  E  L  L  O
Keyword:    K  E  Y  K  E  (repeated)
Ciphertext: R  I  J  V  S
```
Same plaintext letter → different ciphertext letters! Broken by **Kasiski Examination** (finds keyword length via repeated patterns).

## 9. Vernam Cipher
Key = SAME LENGTH as message (no repeat). Uses **XOR**.
```
0 XOR 0 = 0   0 XOR 1 = 1   1 XOR 0 = 1   1 XOR 1 = 0
```
XOR is self-reversing (same op encrypts & decrypts). Weak if key is REUSED.

## 10. One-Time Pad (OTP)
Vernam done PERFECTLY:
1. Key truly RANDOM
2. Same LENGTH as message
3. Used only ONCE

Result: mathematically **UNBREAKABLE** (Perfect Secrecy — Claude Shannon, 1949).
Not practical: key distribution/storage for long messages is a nightmare.

## Classical Ciphers Summary

| Cipher | Type | Key | Weakness |
|---|---|---|---|
| Caesar | Substitution | 1 number | Brute force |
| Monoalphabetic | Substitution | Random table | Freq. analysis |
| Playfair | Substitution (pairs) | 5×5 grid | Digraph freq. |
| Hill | Substitution (math) | Matrix | Known-plaintext |
| Rail Fence | Transposition | # rails | Small key space |
| Row Transposition | Transposition | Keyword | Anagramming |
| Double Columnar | Transposition | 2 keywords | Anagramming (harder) |
| Vigenère | Polyalphabetic | Keyword | Kasiski Examination |
| Vernam | Stream (XOR) | Random full-length | Key reuse |
| One-Time Pad | Perfect secrecy | Random, once | Key sharing impractical |

```
Evolution: Substitution → Transposition → Polyalphabetic/Stream → Perfect Secrecy
```

---

# PHASE 5: MODERN CRYPTOGRAPHY

## Symmetric vs Asymmetric

```
SYMMETRIC:
Sender --[encrypt w/ KEY]--> Ciphertext --[decrypt w/ SAME KEY]--> Receiver

ASYMMETRIC:
Sender --[encrypt w/ Receiver's PUBLIC key]--> Ciphertext --[decrypt w/ Receiver's PRIVATE key]--> Receiver
```

| Feature | Symmetric | Asymmetric |
|---|---|---|
| Keys | 1 (same) | 2 (public+private) |
| Speed | Fast | Slow |
| Problem | Key distribution | Solved |
| Examples | AES, DES | RSA, Diffie-Hellman |

**Hybrid systems** (e.g. HTTPS): use Asymmetric to exchange a key, then Symmetric (fast) for actual data.

## Block vs Stream Ciphers

| Block Cipher | Stream Cipher |
|---|---|
| Fixed-size chunks (128-bit) | Continuous bit-by-bit |
| Needs padding | No padding |
| AES | RC4, Vernam Cipher |

## Feistel Cipher Structure

```
Formulas:
L(i) = R(i-1)
R(i) = L(i-1) XOR f(R(i-1), K(i))
```

```
        L(i-1)              R(i-1)
          |                    |
          |                    ↓
          |              [Round Function f] ← K(i)
          |                    |
          ↓                    ↓
        (XOR) <----------------
          ↓                    ↓
        R(i)                 L(i)
```

- Decrypt = same structure, sub-keys in REVERSE order
- Round function `f` does NOT need to be reversible — structure guarantees decryption works
- Design principles: bigger block/key = more secure but slower; more rounds = more secure but slower

## DES (Data Encryption Standard)
- Block = 64 bits | Key = 56 bits (effective) | Rounds = 16

```
Plaintext → Initial Permutation → 16 Feistel Rounds → Swap → Inverse IP → Ciphertext
```

**Round function f:**
```
R(32-bit) → E-Box(expand to 48-bit) → XOR w/ subkey → S-Boxes(48→32-bit, ONLY non-linear step) → P-Box(permute) → f output
```
S-Box lookup: Row = 1st & 6th bit, Column = middle 4 bits.

**Key Schedule:** PC1(drop parity bits) → split C/D → shift left → PC2 → subkey Ki (repeats for 16 rounds)

## AES (Advanced Encryption Standard)
- Block = 128 bits always | Key = 128/192/256 | Rounds = 10/12/14
- NOT Feistel — uses Substitution-Permutation Network (SPN)
- State Matrix filled COLUMN by column

```
Round steps:
SubBytes (S-Box lookup, ONLY non-linear step)
      ↓
ShiftRows (Row1:shift 0, Row2:shift 1, Row3:shift 2, Row4:shift 3)
      ↓
MixColumns (GF(2⁸) math — SKIPPED in final round)
      ↓
AddRoundKey (XOR w/ round key — ONLY step using secret key)
```

### MixColumns Deep-Dive (GF(2⁸) Rules)
```
01 • byte = byte unchanged

02 • byte = shift LEFT 1 bit
            IF original leftmost bit was 1 → XOR result with 1B (0001 1011)
            IF it was 0 → done, no extra step

03 • byte = (02 • byte) XOR (byte)
```
Matrix used:
```
| 02 03 01 01 |
| 01 02 03 01 |
| 01 01 02 03 |
| 03 01 01 02 |
```
Each output byte = XOR of the 4 GF(2⁸) products in that row.

### DES vs AES

| Feature | DES | AES |
|---|---|---|
| Structure | Feistel | SPN |
| Block | 64-bit | 128-bit |
| Rounds | 16 | 10/12/14 |
| Status | Broken | Still secure |

## RSA
- Public Key = (e,n) | Private Key = (d,n)
```
Encrypt: C = M^e mod n
Decrypt: M = C^d mod n
```
**Key Generation:**
```
1. Pick 2 large primes p, q
2. n = p × q
3. φ(n) = (p-1)(q-1)
4. Pick e: coprime with φ(n)
5. Find d: (e×d) mod φ(n) = 1
```
Security: easy to multiply primes, HARD to factor n back into p,q (trapdoor function).
Attacks: Factorization (main threat), brute force, timing attacks, chosen ciphertext.

## Diffie-Hellman Key Exchange
Public: q(prime), a(primitive root). Each picks private XA/XB.

```
YA = a^XA mod q          YB = a^XB mod q
      (exchange YA, YB openly)
A computes: K = YB^XA mod q
B computes: K = YA^XB mod q
         → SAME K!  (since XA×XB = XB×XA)
```
Security: Discrete Logarithm Problem (hard to reverse a^x mod q).
Weakness: no built-in authentication → vulnerable to MITM.

---

# PHASE 6: HASH FUNCTIONS

## Why Hashing Exists
Need a one-way "fingerprint" for integrity checking & password storage — NOT for reversible secrecy like encryption.

```
Input (any size) → [Hash Function] → Output (FIXED size, always same length)
```

## One-Way Function
```
Message → [Hash] → Hash Value     ✅ EASY forward
Hash Value → [???] → Message      ❌ PRACTICALLY IMPOSSIBLE reverse
```

| Feature | Encryption | Hashing |
|---|---|---|
| Reversible? | Yes (w/ key) | No |
| Uses key? | Yes | No |
| Purpose | Confidentiality | Integrity/fingerprint |

## Collision
Two DIFFERENT inputs → SAME hash output. Mathematically unavoidable somewhere (Pigeonhole Principle) but should be extremely hard to find on purpose.

## Avalanche Effect
Tiny input change → COMPLETELY different output.
```
"Hello"  → 8b1a9953c4611296...
"Hello!" → 334825ba6ec4f273...   (totally different!)
```

## Password Hashing
```
Password → [Hash] → Stored hash (NOT the actual password)
Login attempt → [Hash again] → Compare with stored hash → Match = success
```

## Integrity Checking
```
Original file → Hash = H1
File later → Hash = H2
H1 = H2 → unchanged ✅
H1 ≠ H2 → tampered ❌
```

## MD & SHA Family

| Algorithm | Output Size | Status | Use |
|---|---|---|---|
| MD5 | 128-bit | ❌ Broken | Legacy checksums only |
| SHA-1 | 160-bit | ❌ Broken (2017) | Being phased out |
| SHA-256 | 256-bit | ✅ Secure (current standard) | HTTPS, Bitcoin, passwords |
| SHA-512 | 512-bit | ✅ Secure | High-security |
| SHA-3 | Variable | ✅ Secure (new design) | Backup standard |

```
MD5(1991) → SHA-1(1995) → SHA-2/256(2001) → SHA-3(2015, backup)
```

---

# PHASE 7: DIGITAL SIGNATURES

## Purpose
Authentication + Integrity + Non-Repudiation (NOT Confidentiality!)

## Process

```
SENDER (Signing):
Message → [Hash Function] → Hash(H) → [Encrypt w/ Sender's PRIVATE key] → Signature(S)
Send: [Message + Signature S]

RECEIVER (Verification):
Received Message → [Hash Function] → H2 (fresh hash)
Received Signature → [Decrypt w/ Sender's PUBLIC key] → H1 (original hash)
Compare H1 vs H2:
  ✅ Match → Valid (authentic + unchanged)
  ❌ No match → Invalid (tampered or fake)
```

## Properties

| Property | Provided? |
|---|---|
| Authentication | ✅ Yes |
| Integrity | ✅ Yes |
| Non-Repudiation | ✅ Yes |
| Confidentiality | ❌ NO (message sent in plain form!) |

## RSA vs DSA

| Feature | RSA | DSA |
|---|---|---|
| Can encrypt? | ✅ Yes | ❌ No |
| Can sign? | ✅ Yes | ✅ Yes |
| Basis | Prime factorization | Discrete logarithm |

## CA (Preview)
Trusted 3rd party that verifies public keys really belong to who they claim (full detail in Phase 8).

---

# PHASE 8: KEY MANAGEMENT

## Key Lifecycle
- **Key Generation** — must be truly random (predictable = weak)
- **Key Exchange** — Diffie-Hellman or RSA-encrypted symmetric key
- **Key Storage** — HSMs, encrypted storage, access control (Least Privilege)
- **Key Rotation** — periodically replace keys to limit exposure/damage

## PKI (Public Key Infrastructure)
Solves: "How do I know a public key genuinely belongs to who it claims?"

## Certificate Authority (CA) Process

```
Website → generates key pair, sends public key + identity proof → CA
CA verifies identity → signs a Digital Certificate w/ CA's PRIVATE key
Certificate (identity + public key + CA signature) → sent to your browser
Browser verifies CA's signature using CA's PUBLIC key (pre-installed/trusted)
   ✅ Valid → trusts that public key belongs to the website
```

## Chain of Trust

```
Root CA (self-signed, pre-installed, highest trust)
      ↓ signs
Intermediate CA
      ↓ signs
Website's Certificate
```

## Certificate Revocation
If a key is compromised → invalidate certificate before expiry via:
- **CRL** (Certificate Revocation List)
- **OCSP** (Online Certificate Status Protocol)

---

# PHASE 9: AUTHENTICATION & ACCESS CONTROL

## AAA Framework

```
AUTHENTICATION: "Who are you?"        (password, biometric)
      ↓
AUTHORIZATION: "What can you do?"     (check permissions)
      ↓
ACCOUNTING: "What did you do?"        (logs/audit trail)
```

## MFA (Multi-Factor Authentication)
2+ of these 3 factor types:
- Something you KNOW (password)
- Something you HAVE (phone, token)
- Something you ARE (fingerprint/biometric)

## Biometrics
Physical (fingerprint, face, iris) or behavioral (typing rhythm, voice) traits.
Pro: can't be forgotten. Con: can't be "changed" if compromised.

## Access Control Models

| Model | Who Controls Access | Example |
|---|---|---|
| DAC (Discretionary) | Resource owner | Sharing your own files |
| MAC (Mandatory) | Central authority + security labels | Military "Top Secret" |
| RBAC (Role-Based) | User's assigned role | Doctor vs Nurse vs Receptionist |

## Kerberos

```
Parties: Client(C), AS(Authentication Server), TGS(Ticket Granting Server), Service Server(V)

1. Client → AS: "I am Ahmad, want to start session"
2. AS → Client: TGT (encrypted with key from Ahmad's password)
3. Client → TGS: "Here's my TGT, I want File Server access"
4. TGS → Client: Service Ticket (for File Server specifically)
5. Client → Service Server: "Here's my Service Ticket"
6. Service Server: Access Granted
```

**Protection:**
- Eavesdropping: password NEVER sent over network (only used locally to decrypt TGT)
- Replay: tickets have TIMESTAMP + LIFETIME (expired tickets rejected)

## Inter-Realm Authentication
Extends Kerberos trust ACROSS separate realms (e.g., University A ↔ University B), via pre-established trust between each realm's TGS.

```
Client authenticates in Home Realm
      ↓
Requests inter-realm TGT for Foreign Realm's TGS
      ↓
Foreign TGS issues Service Ticket (since realms trust each other)
      ↓
Client accesses Foreign Service normally
```

---

# PHASE 10: SOFTWARE SECURITY

## Buffer Overflow
Writing MORE data into a fixed-size memory buffer than it can hold — overflow spills into adjacent memory, potentially executing attacker's code.

```
Buffer designed for "Hello" (5 chars)
Malicious input: "AAAA...[malicious code]..." → overflows past boundary
```
Defense: input length validation, memory-safe languages, stack canaries.

## Injection Attacks
Tricking a program into executing UNINTENDED commands via malicious input mixed with normal data.
Defense: **Parameterized queries** (input always treated as data, never command logic), input validation, Least Privilege.

## Secure Coding Principles
Input Validation | Least Privilege | Fail-Safe Defaults | Proper Error Handling | Keep software updated | Code review

---

# PHASE 11: MALWARE

| Type | Key Trait | Needs Human Action? | Damage |
|---|---|---|---|
| Virus | Attaches to files | Yes | Corrupts/deletes, spreads via files |
| Worm | Self-replicates over network | No | Spreads fast, eats resources |
| Trojan | Disguised as legit software | Yes (tricked) | Steals data, backdoors |
| Ransomware | Encrypts files, demands payment | Often phishing | Locks victim out |
| Spyware | Secretly monitors/collects data | Bundled w/ software | Steals passwords/info |
| Rootkit | Hides itself, keeps admin access | After initial compromise | Deep hidden control |
| Botnet | Network of infected "bot" PCs | Individual infections combine | DDoS, spam, mining |

```
Botnet → used to launch DDoS attacks (thousands of machines flood target)
```

---

# PHASE 12: DATABASE SECURITY

## Database Threats
Unauthorized access, SQL Injection, data leakage, insider threats, insufficient encryption.

## SQL Injection
Malicious input manipulates a database query's LOGIC.
```
"SELECT * FROM Users WHERE username=[input] AND password=[input]"
```
If input alters query logic → bypass login / leak / delete data.
Defense: **Parameterized queries**, input validation, Least Privilege.

## Access Control in Databases
Table-level, Row-level, Column-level restrictions (RBAC applied to DB structure).

## Backup Strategies
- Full Backup — complete copy
- Incremental Backup — only changes since last backup
- **3-2-1 Rule**: 3 copies, 2 storage types, 1 off-site

## Database Encryption
- Encryption at Rest (stored data)
- Encryption in Transit (data moving)
- Column-level Encryption (specific sensitive columns)
- Passwords should be HASHED, not just encrypted (one-way, even admins can't reverse)

---

# PHASE 13: NETWORK SECURITY

## Secure Communication
Encryption + Authentication + Integrity checks for data in transit (TLS/HTTPS combines everything from this course!).

## Firewall
Monitors/controls traffic based on rules (Fail-Safe Defaults: deny by default).
```
Internet (untrusted) <---> [FIREWALL] <---> Internal Network (trusted)
```

## IDS vs IPS

| Feature | IDS | IPS |
|---|---|---|
| Action | Detect + Alert only | Detect + Actively Block |
| Placement | Passive (monitors copy) | Active (in traffic path) |

## Network Segmentation
Divides network into isolated segments, so breach in one doesn't spread to others (Least Common Mechanism principle).

## VPN & VPN Modes

```
Employee's Laptop --[Encrypted VPN Tunnel]--> Company Network (over public internet)
```

- **Transparent Mode** — security applied invisibly, original packet structure often stays visible
- **Tunneling Mode** — ENTIRE original packet wrapped inside new packet → **closest to actual VPN technique**

```
Original Packet: [Header|Data]
      ↓ Tunneling: wrap entire thing
New Packet: [New Header | ENCRYPTED[Original Header|Data]]
```

## IPsec

| Protocol | Confidentiality | Integrity | Authentication |
|---|---|---|---|
| AH (Authentication Header) | ❌ No | ✅ Yes | ✅ Yes |
| ESP (Encapsulating Security Payload) | ✅ Yes | ✅ Yes | ✅ Yes |

**IPsec Modes:**
- Transport Mode — protects only the DATA payload, original IP header stays visible (used device-to-device)
- Tunnel Mode — protects ENTIRE packet, new outer header (used for VPNs, Site-to-Site)

Most VPNs use: **ESP + Tunnel Mode** together.

---

# PHASE 14: SECURITY POLICIES

| Term | Meaning |
|---|---|
| Policy | General rule ("be secure") — high-level |
| Standard | Mandatory specific technical requirement |
| Guideline | Optional suggestion |
| Procedure | Step-by-step how-to |

```
Policy Development: identify what to protect → write rules → get approval → share with everyone
Policy Enforcement: training + monitoring + consequences
```

---

# PHASE 15: RISK MANAGEMENT

```
1. Risk Identification   → "What could go wrong?"
2. Risk Assessment       → "How likely + how bad?" (Low/Med/High)
3. Risk Analysis         → Rank/compare risks (Risk = Threat × Vulnerability × Impact)
4. Risk Treatment        → Avoid / Reduce / Transfer / Accept
5. Business Impact Analysis → "How much would it cost the business?"
```

## Risk Treatment Options

| Option | Meaning | Example |
|---|---|---|
| Avoid | Stop doing the risky thing | Don't store card numbers if not needed |
| Reduce | Lower the risk | Firewall, training, encryption |
| Transfer | Let someone else handle it | Cyber-insurance |
| Accept | Small risk, not worth fixing | Old rarely-used low-value file |

---

# PHASE 16: CYBERCRIME, LAW & ETHICS

| Concept | Meaning |
|---|---|
| Cybercrime | Crime done using/against computers |
| Digital Evidence | Data used in court — needs **Chain of Custody** (proves it wasn't tampered with) |
| Ethical Responsibilities | Use security knowledge responsibly (Ethical Hacker vs Black Hat) |
| Privacy | Person's RIGHT to control their own information |
| Data Protection | Laws/practices protecting personal data (e.g., GDPR) |
| Anonymity | Hiding identity online — double-edged sword (protects whistleblowers, but also helps criminals) |

---

# PHASE 17: ADVANCED TOPICS

## Cloud Security — Shared Responsibility Model
```
Cloud Provider's Job:  Secure physical servers, data centers, infrastructure
Your Job (User):       Secure YOUR data, access controls, configurations
```

## IoT Security
Everyday smart devices (cameras, TVs) often weakly secured (default passwords, no updates) → major source of botnets.

## Zero Trust Model
**Motto: "Never trust, always verify."** Every request checked every time, even from users already inside the network (connects to Complete Mediation, Phase 2).

## DevSecOps
Build security checks INTO the fast software development pipeline from the start, not bolted on at the end.
```
Old way:   Code → Test → Release → (security checked too late)
DevSecOps: Code → Security Check (automatic) → Test → Release
```

---

# 🎯 END OF NOTES — Full Course Covered
### Phases 1–17 | Classical + Modern Cryptography | Networks | Management | Ethics
