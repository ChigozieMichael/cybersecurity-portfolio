# PCAP Forensic Analysis – Dridex Banking Trojan Infection

## Overview
This project documents a complete forensic investigation of a network capture containing a confirmed Dridex banking trojan infection. Analysis was performed on the file `traffic-with-dridex-infection.pcap`, covering 42 minutes of network activity. The investigation identifies Command and Control (C2) servers, malicious domains, payload downloads, exfiltration indicators, and the full attack timeline.

**Analyst:** Chigozie Michael Okereke  
**Date:** October 15, 2025  
**Incident Type:** Banking Trojan (Dridex)  
**Tools:** Wireshark, Tshark, VirusTotal, Threat Intel Sources

---------------------------------------------------------------------

## Executive Summary
Analysis confirms a **critical malware infection** involving the Dridex banking trojan. The victim machine communicated with at least seven malicious external IPs, downloaded multiple payloads, and performed persistent C2 beaconing. Evidence strongly suggests credential theft, exfiltration of financial data, and high risk of further compromise.

Key outcomes:
- 7 malicious IPs identified  
- 3 malicious domains  
- Dridex payload downloaded (.rar)  
- Persistent C2 communication (378 beacons)  
- Signs of keylogging, credential theft, and staged exfiltration  

**Severity: CRITICAL – Immediate containment required**

---------------------------------------------------------------------

## File Analyzed
`traffic-with-dridex-infection.pcap`  
Duration: 42 minutes of traffic  
Host OS: Windows 7 (based on User-Agent evidence)

---------------------------------------------------------------------

## Task-by-Task Findings

### Task 1: External IP Address Analysis
Most frequent malicious external IPs communicating with the infected host:

| Rank | IP Address       | Connections | Status           |
|------|------------------|-------------|------------------|
| 1    | 83.166.247.211   | 378         | Primary C2       |
| 2    | 176.32.33.108    | 156         | Secondary C2     |
| 3    | 95.181.198.231   | 152         | Payload Server   |
| 4    | 172.106.33.46    | 40          | Malicious        |
| 5    | 185.244.150.230  | 39          | Malicious        |
| 6    | 185.158.251.55   | 39          | Malicious        |
| 7    | 174.34.253.11    | 39          | Malicious        |

**Bottom Line:**
- 7 malicious IPs  
- 3 malicious domains  
- Persistent C2 communication  
- High-risk payloads transferred

PoC Showing Wireshark analysis of top conversations 
<img width="792" height="241" alt="image" src="https://github.com/user-attachments/assets/d3eacb9e-c252-4674-87f5-1e3be6f6e38c" />

**Recommendation:** Block all listed IPs at firewall immediately.

---------------------------------------------------------------------

### Task 2: HTTP Traffic Analysis

#### Suspicious User-Agent Strings
Observed multiple outdated and inconsistent User-Agents:
- IE 7 on Windows NT 6.1
- IE 8 on Windows NT 6.1
- Trident/7.0
PoC Showing Tshark analysis of  Suspicious User-Agent Strings. 
<img width="792" height="152" alt="image" src="https://github.com/user-attachments/assets/9fb5528b-bc7d-44b2-a027-27232d8765ab" />

**Red Flags:**
- Indicators of malware automation  
- No modern browsers present  
- User-Agent spoofing to evade detection  

#### Suspicious HTTP Requests
Initial infection request:
- GET klychenogg.com/QIC/tewokl.php?l=spet10.spr
Payload downloads:
- GET cochrimato.com/images/[random].avi
- GET 95.181.198.231/oiioiashdqbwe.rar


**Insight:**  
Fake `.avi` files and encoded URLs commonly hide malware components.

---------------------------------------------------------------------

### Task 3: DNS Query Analysis

| Domain            | Queries | Assessment                       |
|-------------------|---------|----------------------------------|
| cochrimato.com    | 2       | Malicious – payload hosting      |
| mautergase.com    | 4       | Malicious – C2                   |
| klychenogg.com    | 2       | Malicious – initial infection    |
| myip.opendns.com  | 4       | Legit – IP check                 |
| resolver1.opendns.com | 3   | Legit                            |

PoC Showing DNS Query Analysis 
<img width="660" height="179" alt="image" src="https://github.com/user-attachments/assets/2371cc10-de08-4990-934b-2302a09152e9" />

**Indicators of DGA (Domain Generation Algorithm):**
- Randomized naming  
- Disposable infrastructure  
- Obfuscated requests  

**Recommendation:** Block domains at DNS level; monitor for similar patterns.

---------------------------------------------------------------------

### Task 4: File Upload/Download Analysis
Files downloaded:

**1. `oiioiashdqbwe.rar`**  
- Dridex payload  
- Direct IP download  
- Likely delivered via macro-enabled document  

**2. Fake `.avi` files**  
- Masquerade as video content  
- Contain obfuscated malware code  
- Multiple fallback URLs indicate redundancy

- PoC Of  Wireshark filter of  HTTP upload/download analysis 
<img width="792" height="305" alt="image" src="https://github.com/user-attachments/assets/d2ff0025-d2d4-40d4-91e4-cf5af44e6253" />

**Recommendation:**  
- Extract + hash files  
- Submit to VirusTotal  
- Investigate any related samples across fleet systems

---------------------------------------------------------------------

### Task 5: Protocol & Behavioral Anomaly Analysis

**Standard Port Usage:**  
Malware used only HTTP (80), HTTPS (443), DNS (53).  
This is a deliberate stealth technique.

PoC Of Tshark Protocol Anomaly Analysis
<img width="792" height="354" alt="image" src="https://github.com/user-attachments/assets/0961e55e-b63c-44e8-9c47-207613be7ccc" />

**Behavioral Anomalies Identified:**
- Direct-to-IP HTTP requests (bypassing DNS)  
- 378 repeated connections to single C2  
- Long-lived beaconing  
- Rapid sequential payload downloads  
- Encrypted C2 via HTTPS  

**Detection Strategy:**  
Signature-based detection fails.  
Behavioral detection required:
- Beacon curves  
- User-agent anomaly rules  
- DNS entropy scoring  
- Volume-based anomaly rules

---------------------------------------------------------------------

## Full Attack Timeline (Dridex Sequence)

**Phase 1: Delivery**  
- Malicious email with macro-enabled document  

**Phase 2: Execution**  
- User opens file  
- Macro executes, contacts dropper URL  

**Phase 3: Installation**  
- Fake `.avi` downloads  
- Primary payload `.rar` downloaded  
- Persistence created  

**Phase 4: Command & Control**  
- Beaconing to 83.166.247.211  
- Backup C2 servers contacted  
- Commands received from operator  

**Phase 5: Actions on Objectives**  
- Keylogging  
- Credential harvesting  
- Banking session hijack  
- Data exfiltration  

---------------------------------------------------------------------

## Indicators of Compromise (IOCs)

### Malicious IPs (Priority 1)
- 83.166.247.211  
- 176.32.33.108  
- 95.181.198.231  

### Malicious IPs (Priority 2)
- 172.106.33.46  
- 185.244.150.230  
- 185.158.251.55  
- 174.34.253.11  

### Malicious Domains
- mautergase.com  
- klychenogg.com  
- cochrimato.com  

### Malicious URLs
- http://klychenogg.com/QIC/tewokl.php?l=spet10.spr  
- http://95.181.198.231/oiioiashdqbwe.rar  
- http://cochrimato.com/images/[random].avi  

### Malicious Files
- oiioiashdqbwe.rar  
- tewokl.php  
- [various].avi (fake video files)

---------------------------------------------------------------------

## Impact Assessment

### Victim Machine Impact: CRITICAL
- Keylogging  
- Credential theft  
- Browser hijack  
- Persistence mechanisms active  
- High risk of secondary payloads  

### Network Impact: HIGH
- Lateral movement possible  
- Domain credential exposure  
- Bandwidth exhaustion  

### Organizational Impact
- Financial theft  
- GDPR/PCI-DSS breach  
- Reputational damage  
- System rebuilds required  

---------------------------------------------------------------------

## Immediate Actions Required

**Next 4 Hours**
1. Isolate infected system  
2. Block all 7 malicious IPs  
3. Disable compromised accounts  
4. Block malicious domains  
5. Preserve forensic evidence  
6. Notify IT Security team  

**Next 24 Hours**
7. Rebuild infected machine  
8. Deploy EDR  
9. Review email logs  
10. Update antivirus signatures  
11. Implement email sandboxing  

---------------------------------------------------------------------

## Recommended Security Improvements

### Endpoint Controls
- Deploy EDR (CrowdStrike, SentinelOne, Defender ATP)  
- Enable PowerShell logging  
- Application whitelisting  

### Network Controls
- IDS/IPS with Dridex signatures  
- DNS filtering  
- TLS inspection  
- Network segmentation  

### Email Security
- Block macro-enabled attachments  
- DMARC/SPF/DKIM enforcement  
- Sandboxing & attachment scanning  

### Access Controls
- Enforce MFA  
- Privileged Access Management  
- Least privilege implementation  

### Process Improvements
- Update Incident Response playbooks  
- User awareness training  
- Regular threat hunting and PCAP analysis  

---------------------------------------------------------------------

## Lessons Learned

**What Failed**
- Email filtering  
- User awareness  
- Endpoint protection  
- Real-time network detection  

**What Worked**
- PCAP capture availability  
- DNS-level visibility  
- Network segmentation  

---------------------------------------------------------------------

## Conclusion
The analysis confirms a severe Dridex malware compromise with:
- Persistent C2 communication  
- Credential theft  
- Multiple payload downloads  
- High-confidence exfiltration indicators  

**Severity: CRITICAL – Immediate remediation required.**

---------------------------------------------------------------------

## Evidence
Screenshots from:
- Wireshark  
- Tshark  
- Dridex payload analysis  
- C2 communication graphs  
- Suspicious HTTP requests  
- DNS query logs  
