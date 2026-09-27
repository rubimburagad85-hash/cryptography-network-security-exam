# Risk Assessment – ULK Polytechnic Institute Student Records

**Scenario summary:** Student records are held on a central server and transferred between two campuses. A security review found weak staff passwords, outdated software, unencrypted file transfers and guest-network access to the records server. Repeated connection attempts from an unfamiliar external address have also been observed.

## a. Assets, vulnerabilities and consequences

| # | Asset | Vulnerability | Possible consequence |
|---|-------|---------------|----------------------|
| R1 | Staff accounts / credentials (gateway to all records) | **Weak staff passwords**, made worse by repeated external login attempts (brute-force / credential-guessing) | Account takeover; attacker reads, changes or deletes student records; loss of confidentiality **and** integrity; attacker acts under a staff identity, so misuse is hard to trace |
| R2 | Student records server (database and files) | **Guest network can reach the server** (no segmentation) and runs **outdated software** | Any guest device can scan and exploit known flaws; data breach, ransomware, service outage; possible breach of data-protection duties and loss of institutional reputation |
| R3 | Student record files in transit between the two campuses | **Unencrypted file transfers** | Eavesdropping or man-in-the-middle on the inter-campus link; records read or silently altered in transit; loss of confidentiality and integrity |

## b. Risk ranking (likelihood × impact, scale 1 = low … 5 = high)

| Rank | Risk | Likelihood | Impact | Score | Reasoning |
|------|------|-----------:|-------:|------:|-----------|
| 1 | R1 – Weak staff passwords | 5 | 5 | **25** | Attacks are already happening (repeated attempts from an unfamiliar address). Guessing weak passwords needs little skill, and one compromised account exposes everything that user can reach. |
| 2 | R2 – Guest access to records server + outdated software | 4 | 5 | **20** | Guest users are numerous and untrusted, and only need to be on the Wi-Fi. Known exploits exist for outdated software. Impact is very high because the whole record set sits on this server. |
| 3 | R3 – Unencrypted inter-campus transfers | 3 | 4 | **12** | The attacker needs a position on the network path between campuses (harder than guessing a password), but any successful interception exposes complete files and allows tampering. |

## c. Recommended controls

| Risk | Control | How it reduces the risk |
|------|---------|-------------------------|
| R1 | **Strong password policy + multi-factor authentication** (minimum 12 characters, no reuse, account lockout / rate limiting, e.g. fail2ban) | A guessed or stolen password alone is no longer enough; brute-force attempts are slowed or stopped. |
| R2 | **Network segmentation and firewall rules** (guest VLAN denied to the server; only the staff network reaches the required service) plus a **patch-management** schedule | Removes the attack path from guests; patching closes known vulnerabilities. Implemented in `firewall/records_server_firewall.sh`. |
| R3 | **Encrypt files before and during transfer** (AES-256-GCM via `src/secure_records.py`; SFTP/TLS/VPN for the link) with SHA-256 integrity checks | Interceptors see only ciphertext; any modification is detected. Implemented in `src/secure_records.py`. |

*Note:* the unfamiliar external address is handled by the firewall control (R2) and the MFA/lockout control (R1).
