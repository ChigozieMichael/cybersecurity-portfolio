# Email Security Analysis – Steganographic Python Payload in Phishing Attack 

## Overview
This project documents a complete forensic and threat intelligence analysis of a sophisticated Business Email Compromise (BEC) phishing attack that used steganography to deliver malware. The malicious payload was hidden inside a JPEG image attachment and extracted using steganographic cracking tools. The investigation included email header analysis, authentication validation, IOC extraction, malware behavior analysis, and MITRE ATT&CK mapping.

**Analyst:** Chigozie Michael Okereke  
**Date:** October 27, 2025  
**Threat Level:** CRITICAL (9.5/10)  
**Case Status:** Confirmed Malicious  
**Attachment Payload:** Hidden Python script (exec.py) extracted from image

---------------------------------------------------------------------

## Executive Summary
A multi-stage phishing attack impersonating a law firm attempted to deceive the target using a “PAYMENT CONFIRMATION” lure. The attacker embedded a Python malware script inside a JPEG file using steganography. All email authentication checks (DKIM, SPF, DMARC) passed successfully, confirming that the attacker used a compromised legitimate Gmail account.

Key findings:
- Hidden Python script embedded via **steganography**
- Weak passphrase ("password") used for hiding contents
- Image filename shows tampering (“receipt_mod.jpg”)
- Successfully extracted malicious Python script using StegSeek
- Script performs persistence, obfuscation, logging, and potential system compromise
- Email originated from a **compromised** Gmail account, not a spoofed one

**Severity:** CRITICAL – capable of credential theft, persistence, and staged financial fraud.

---------------------------------------------------------------------

## Email Metadata Summary

| Field | Value | Risk |
|-------|--------|-------|
| From | avocadoattorney1@gmail.com | Suspicious, fake identity |
| Subject | PAYMENT CONFIRMATION!!!!! | Urgency tactic |
| To | c******g@gmail.com | Intended victim |
| DKIM/SPF/DMARC | All PASS | Indicates compromised Gmail account |
| Attachment | receipt_mod.jpg | Modified, suspicious image |

**Critical Insight:**  
Passing DKIM, SPF, and DMARC means the attacker used a hacked Gmail account. This bypasses basic security filters and increases the credibility of the phishing attempt.

---------------------------------------------------------------------

## Social Engineering Analysis

Attack tactics included:
- Authority impersonation (“our firm”)  
- Urgency and pressure (“CONFIRMATION!!!!!”)  
- Vague details to increase victim applicability  
- Generic greeting (“Dear Subscriber”)  
- No payment references, invoice numbers, or legal disclaimers  
- Manipulated filename indicating tampering

Objective of the email was to lure the victim into opening the malicious JPEG attachment.

---------------------------------------------------------------------

## Attachment Analysis and Steganography Findings

### Suspicious Indicators
- Filename ends with `_mod` suggesting modification
- Size unusually large for a receipt image (~160 KB)
- Encoded with Base64 in transport

### Extraction Process
Tool used: **StegSeek**  
Wordlist: **rockyou.txt**

**Passphrase cracked:**  
       -password

**Embedded file discovered:**  
      -exec.py
      
PoC OF THE RECEIPT.MOD STEGHIDE ANALYSIS and EXTRACTION OF THE EMBEDDED FILE 
<img width="680" height="191" alt="image" src="https://github.com/user-attachments/assets/363aabc3-deae-4a01-b7bb-deb69d95b664" />

This confirms the malicious actor used steganography to hide malware inside an image.

---------------------------------------------------------------------

## Malware (exec.py) Behavioral Analysis

PoC Of  Extracted Python Script (exec.py)
<img width="619" height="456" alt="image" src="https://github.com/user-attachments/assets/439d54e0-5cad-4230-b1f4-34600045413b" />

### Capabilities Identified

1. **Directory Manipulation**
   - Creates hidden folder `.localdata`
   - Establishes persistence environment

2. **Activity Logging**
   - Logs process IDs
   - Writes to `report_log.txt`

3. **Base64 Obfuscation**
   - Hides filenames
   - Evades signature-based detection

4. **Delay Functions**
   - Sleeps to evade sandboxes and automated analysis

5. **Persistence Mechanism**
   - Creates marker files for reinfection
   - Enables long-term control of compromised systems

6. **Potential for Escalation**
   - Combined with BEC, possible full account compromise and financial diversion

---------------------------------------------------------------------

## MITRE ATT&CK Mapping

| Tactic | Technique | ID | Evidence |
|--------|-----------|-----|----------|
| Initial Access | Spearphishing Attachment | T1566.001 | Malicious JPEG |
| Initial Access | Spearphishing Link | T1566.002 | Lure text |
| Defense Evasion | Steganography | T1027.003 | Hidden Python script |
| Defense Evasion | Obfuscated Files | T1027 | Base64 encoding |
| Defense Evasion | Masquerading | T1036 | Fake law firm identity |
| Execution | Command & Scripting Interpreter (Python) | T1059.006 | `exec.py` |
| Persistence | Create/Modify System Process | T1543 | Marker files |
| Discovery | System Information Discovery | T1082 | PID logging |

This attack demonstrates multiple MITRE mappings, reinforcing its sophistication.

---------------------------------------------------------------------

## Indicators of Compromise (IOCs)

### Malicious Domains / Emails
- avocadoattorney1@gmail.com (compromised Gmail account)

### Attachment
- `receipt_mod.jpg` (steganographic container)

### Extracted Malware
- `exec.py` (Python payload)

### Behavioral Indicators
- Hidden directory creation
- Base64 filename obfuscation
- Unauthorized log file creation
- Suspicious timing behavior patterns

---------------------------------------------------------------------

## Risk Assessment

Impact Level:
- **Confidentiality:** CRITICAL  
  Potential for credential theft and data exfiltration  

- **Integrity:** HIGH  
  Directory and file manipulation

- **Availability:** MEDIUM  
  Could escalate into ransomware  

- **Financial:** CRITICAL  
  BEC fraud implications

---------------------------------------------------------------------

## Recommendations

### Immediate (0–2 hours)
- Block sender domain (gmail account involved)
- Isolate affected system
- Delete malicious email from all mailboxes
- Notify security team and management

### 24-Hour Action Items
- Enable sandboxing for all attachments
- Implement advanced anti-phishing filters
- Enforce company-wide MFA
- Reset potentially compromised email accounts

### One-Week Improvements
- Conduct phishing simulation training
- Deploy endpoint monitoring and EDR
- Strengthen password policies and enforce password managers
- Review secure email gateway configuration

### Long-Term
- Enable strict DMARC policy (reject)
- Integrate threat intelligence feeds
- Conduct regular steganography-based threat tests
- Build incident response playbooks for email-based attacks.


## Status
Completed and validated as part of cybersecurity threat analysis and forensic investigation project.


## Lessons Learned
- Modern phishing attacks often use compromised accounts instead of spoofing.
- Steganographic payload delivery is becoming more common and evades signature-based scanning.
- End users remain a critical vulnerability in BEC fraud operations.
- A defense-in-depth approach is necessary to reduce the attack surface.
- Logs and forensic captures are essential for accurate threat classification and response.

       
