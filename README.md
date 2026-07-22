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
- **Tools Used:** `tshark`, Network Miner, Command Line (Bash/PowerShell)
- **Protocols Investigated:** TCP, UDP, DNS, HTTP/HTTPS, ICMP

---

## 🕵️ Incident Investigation & Analysis

### Phase 1: Reconnaissance Detection (Port Scanning)
While monitoring internal subnet traffic, an anomalous high volume of `SYN` packets was observed originating from a single internal host.

* **Target Subnet:** `192.168.1.0/24`
* **Suspicious Source IP:** `192.168.1.105`
* **Wireshark Filter Used:** 
  ```text
  ip.src == 192.168.1.105 && tcp.flags.syn == 1 && tcp.flags.ack == 0
