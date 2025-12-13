This project demonstrates my ability to perform security monitoring, event investigation, and brute-force attack detection using Splunk Enterprise. I analyzed Windows Security EventLogs (EventCode 4625 – Failed Logon) to identify suspicious authentication behavior, source IP addresses, failure reasons, time-based patterns, and potentially malicious attempts to discover usernames.

**The project includes:**

Threshold-based brute-force detection

FailureReason analysis (%%2304 / %%2313)

Identity enumeration analysis (TargetUserName / TargetDomainName)

SOC-style timechart visualizations

Correlation searches written in SPL



**Tools Used**

Splunk Enterprise 10.0.2

Windows Security Event Logs

XML audit logs (sourcetype=XmlWinEventLog)

SPL (Search Processing Language)


**Project Objectives**

Detect brute-force login attempts by analyzing failed authentication bursts.

Identify which usernames were targeted in login failures.

Determine whether the attempts indicate:

wrong passwords

invalid/non-existent usernames

potential enumeration

intentional probing

Visualize all authentication failures over time.

Build SOC-ready queries and dashboards.



**DATA SOURCE**

Index: main

Sourcetype: XmlWinEventLog

EventCode: 4625 (Failed Logon)


**1. Brute-Force Detection by 5-Minute Time Window**
SPL Query Used...

index=main EventCode=4625
| bin _time span=5m
| stats count by _time, IpAddress
| where count >= 3
| sort - count

What This Shows

This query detects any IP address producing ≥ 3 failed logon attempts in 5 minutes, which is a common brute-force indicator.
<img width="937" height="293" alt="Splunk Brute Force Attempt Detection" src="https://github.com/user-attachments/assets/01e8929f-e08a-476e-931e-3a08cc2684a4" />


**2. Failure Reason Analysis (%%2304 / %%2313)**
SPL Query Used..

index=main sourcetype="XmlWinEventLog" EventCode=4625
| timechart span=1h count by FailureReason

**What This Shows**

This chart shows the frequency of different types of authentication failures over time.
<img width="945" height="439" alt="splunk Failure Reason Timechart" src="https://github.com/user-attachments/assets/3118c94b-6bb5-402b-91aa-9037a3d12231" />

**Interpretation**

%%2304 = Invalid username / User does not exist

%%2313 = Wrong password

This helps differentiate:

Username enumeration attempts

Password-guessing attempts

Misconfigured systems


**3. Identity Enumeration Detection (TargetUserName + TargetDomainName)**
New SPL Query..

index=main sourcetype="XmlWinEventLog" EventCode=4625
| stats count by TargetUserName, TargetDomainName, IpAddress, FailureReason
| sort - count

**Purpose of This Query**

This correlation search reveals:

**Field**                     	**What It Tells the SOC Analyst**

TargetUserName              	Which username attackers attempted to access
TargetDomainName            	Domain/workstation targeted
IpAddress                   	Source of the failed login
FailureReason           	    Why the authentication failed

**This is used in SOC to identify:**

Account enumeration

High-interest users being targeted

Incorrect passwords

Suspicious login origins
<img width="944" height="260" alt="Splunk Identity Enumeration and Failed Logon Correlation" src="https://github.com/user-attachments/assets/b12a9f26-48f0-4714-8c7f-9e4e8ed1006d" />

Screenshot Interpretation

My result shows:

TargetUserName: -

TargetDomainName: -

IpAddress: 127.0.0.1

FailureReason: %%2304

count: 4

This indicates:

An account name was missing or invalid

4 attempts came from localhost

Username enumeration activity may have occurred

Even internal enumeration is relevant for learning SOC workflows—because the same pattern appears during external brute-force attempts.



**4. SOC Analyst Interpretation**

From all three searches, the following insights were identified:

**Indicators of Attack Behavior**

Multiple failed authentication attempts within 5 minutes from the same IP

FailureReason %%2304, meaning invalid usernames → suggests enumeration

Failed attempts repeated in hourly windows

No successful authentication following the failures


**Possible Threat Scenarios**

Brute-force attack using guessed passwords

Username harvesting

Misconfigured application repeatedly attempting login

Insider mischief or unintended user error

All of these are relevant SOC alert categories.



**Recommendations**

Implement account lockout threshold (3–5 attempts).

Enable Sysmon for enriched telemetry.

Add detect/alert rule in Splunk:

Recommended Alert Trigger
count >= 3 by 5-minute window


Enforce password policy and MFA.

Review workstation authentication logs daily.

Honeypots like Fail2ban can be deployed.
