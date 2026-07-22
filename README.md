# 🔍 Wireshark Network Traffic Analysis & Incident Response Lab

![Wireshark](https://img.shields.io/badge/Wireshark-167DA4?style=for-the-badge&logo=wireshark&logoColor=white)
![Network Security](https://img.shields.io/badge/Network_Security-000000?style=for-the-badge&logo=shield&logoColor=white)
![Incident Response](https://img.shields.io/badge/Incident_Response-E10098?style=for-the-badge&logo=target&logoColor=white)

## 📌 Executive Summary
This technical laboratory analysis documents the detection, isolation, and remediation of malicious network traffic captured within an enterprise local area network (LAN). Using **Wireshark** and packet filtering techniques, I analyzed raw `.pcap` data to identify an unauthorized Port Scan reconnaissance attempt followed by a Command-and-Control (C2) beaconing session.

The goal of this write-up is to detail the threat investigation lifecycle, from initial packet inspection to actionable firewall rule mitigation.

---

## 🛠️ Lab Environment & Tools
- **Packet Analyzer:** Wireshark v4.x
- **Network Topology:** Enterprise Dual-Subnet LAN (192.168.1.0/24)
- **Tools Used:** tshark, Network Miner, Command Line (Bash/PowerShell)
- **Protocols Investigated:** TCP, UDP, DNS, HTTP/HTTPS, ICMP

---

## 🕵️ Incident Investigation & Analysis

### Phase 1: Reconnaissance Detection (Port Scanning)
While monitoring internal subnet traffic, an anomalous high volume of SYN packets was observed originating from a single internal host.

- **Target Subnet:** `192.168.1.0/24`
- **Suspicious Source IP:** `192.168.1.105`
- **Wireshark Filter Used:** 
  `ip.src == 192.168.1.105 && tcp.flags.syn == 1 && tcp.flags.ack == 0`
- **Observation:** The host generated over 1,200 SYN requests across sequential destination ports within 15 seconds, indicating an active **Nmap SYN Stealth Scan**.

---

### Phase 2: Traffic Inspection & Threat Identification
Following the port scan, packet streams were inspected to determine if exploitation or outbound data exfiltration occurred.

1. **DNS Query Inspection:**
   - **Filter:** `dns.flags.response == 0`
   - **Finding:** A high frequency of rapid, high-entropy subdomains was observed targeting `evil-c2-domain[.]com` (e.g., `a1x9f.evil-c2-domain[.]com`), indicative of **DNS Tunneling / Data Exfiltration**.

2. **HTTP/Unencrypted Stream Following:**
   - **Filter:** `http.request.method == "POST"`
   - **Finding:** Following the TCP stream revealed clear-text HTTP POST requests carrying base64-encoded payloads transmitted to port `8080` on an external IP (`203.0.113.45`).

---

## 📊 Summary of Indicators of Compromise (IoCs)

| Indicator Type | Value / Payload | Threat Classification |
| :--- | :--- | :--- |
| **Internal Malicious IP** | `192.168.1.105` | Compromised Internal Host |
| **External Attacker IP** | `203.0.113.45` | Command & Control (C2) Server |
| **Malicious Domain** | `evil-c2-domain[.]com` | DNS Tunneling / Exfiltration |
| **Targeted Ports** | TCP `80`, `443`, `8080`, `22` | Service Enumeration |

---

## 🛡️ Mitigation & Remediation Recommendations

Based on the findings, the following short-term and long-term security controls were formulated for network implementation:

1. **Host Isolation:**
   - Immediately isolate workstation `192.168.1.105` from the VLAN via switch port shutdown / network-level quarantining.

2. **Firewall Rule Implementation:**
   - Block all inbound and outbound traffic to/from external IP `203.0.113.45`.
   - Implement egress filtering on edge routers (MikroTik/Cisco) to restrict outbound TCP port `8080` except for authorized gateways.

3. **DNS Security Enhancements:**
   - Deploy DNS Sinkholing for `evil-c2-domain[.]com`.
   - Enforce internal DNS resolution strictly through secured recursive resolvers running DNSSEC.

---

## 🧑‍💻 Author & Contact
- **Analyzed By:** Jeffrey M. Nadado | IT Specialist & Civil Service Professional
- **Certifications:** TESDA Cybersecurity Monitoring, Cisco NetAcad Junior Cybersecurity Analyst
