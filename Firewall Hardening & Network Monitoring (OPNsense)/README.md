Firewall Hardening & Network Monitoring (OPNsense)

This project demonstrates hands-on firewall security engineering using OPNsense, covering:

Network segmentation (LAN/LAN2/WAN separation)

Rule enforcement for least-privilege

Blocking unauthorized lateral movement

DNS filtering and DHCP monitoring

NAT rule design and verification

Real-time firewall log analysis

IPv4/IPv6 traffic inspection

Event correlation for intrusion identification


   1: Baseline Firewall Policy – Deny-All Default Stance

I enforced a default-deny security model by blocking all traffic unless explicitly allowed.

Screenshot: Default block all traffic
<img width="402" height="199" alt="Default block rule" src="https://github.com/user-attachments/assets/7dd278ea-349c-478a-9c8a-34f9ebe83728" />

  
   2: Enabling Comprehensive Interface Logging

To support SOC visibility and forensic investigations, I enabled logs for:

Default block rules

Pass rules

Bogon networks

Private networks

NAT rules

Screenshot: Enabling logs for all interfaces
<img width="539" height="303" alt="Enable Logs" src="https://github.com/user-attachments/assets/436f106c-5a10-4715-83b5-d4d9ff14bc3c" />


   3: LAN Firewall Rules (DNS, DHCP, SSH Hardening)

This section shows explicit rule creation for:

Allowing LAN → DNS (53/UDP, 53/TCP)

Allowing LAN → DHCP

Blocking LAN users from SSH access to the firewall

These prevent unauthorized admin access and restrict lateral attack vectors.

Screenshot: LAN rules with SSH blocking
<img width="571" height="361" alt="LAN rules with SSH blocking" src="https://github.com/user-attachments/assets/75cacdf7-5ee3-4f62-8d13-010f2cc3f445" />


   4: LAN2 UDP Blocking – Identifying Suspicious Traffic

By inspecting Live View logs, I identified & blocked abnormal LAN2 UDP traffic (destination: 192.168.1.1:53).

This simulates DNS misuse or potential tunneling attempts.

Screenshot: LAN2 UDP blocking logs
<img width="569" height="367" alt="LAN2 UDP blocking" src="https://github.com/user-attachments/assets/964d53d7-4346-49b4-9795-9b3889a9c31e" />


   5: NAT Configuration & Network Segmentation

I configured hybrid outbound NAT to allow controlled routing between:

WAN ↔ LAN

WAN ↔ LAN2

This ensures both networks can reach the internet while maintaining internal segmentation.

Screenshot: NAT outbound rules
<img width="442" height="340" alt="NAT outbound rules" src="https://github.com/user-attachments/assets/f2a15531-9c63-4bfc-8fb3-a270a6ea3142" />


   6: Firewall Event Monitoring – Real-Time Intrusion Visibility

The Live View logs capture:

Blocked internal scanning

UDP floods

Unauthorized access attempts

DHCPv6 traffic

IPv6 ICMP RFC4890 compliance messages

WAN → LAN denied connections

This demonstrates SOC-style traffic investigation and forensic monitoring.

Screenshot: Firewall traffic blocks (multiple events)
<img width="584" height="368" alt="opnsense blocks" src="https://github.com/user-attachments/assets/528c8677-7dcb-4a5f-8fce-353d3ace565d" />
