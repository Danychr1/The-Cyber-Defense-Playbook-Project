# 🔒 The Cyber Defense Playbook: Network Intrusion Analysis  

---

## 🧩 Overview  
This project simulates a **Security Operations Center (SOC) investigation** focused on detecting, analyzing, and responding to network threats.  
It demonstrates how I apply **core SOC skills** such as log analysis, packet inspection, threat hunting, and incident response documentation.

**Objective:**  
Identify suspicious network activity, analyze the attack pattern, and provide recommendations to strengthen the organization’s security posture.

---

## 🧰 Tools & Technologies  
| Category | Tools Used |
|-----------|-------------|
| Network Analysis | Wireshark, tcpdump, Zeek |
| Threat Hunting | Splunk, ELK Stack, Security Onion |
| Scanning & Recon | Nmap, Masscan |
| Threat Intelligence | VirusTotal, AbuseIPDB |
| OSINT & Reporting | MITRE ATT&CK, Markdown, PowerPoint |

---

## ⚙️ Methodology  

### 1. **Data Collection**
- Captured live network traffic using `tcpdump`  
- Imported `.pcap` files into **Wireshark** for packet-level analysis  
- Extracted logs from **Zeek** for higher-level insights

### 2. **Analysis**
- Applied filters to identify HTTP, DNS, and suspicious TCP connections  
- Correlated alerts in **Security Onion** with MITRE ATT&CK techniques  
- Detected lateral movement and beaconing patterns

### 3. **Investigation & Correlation**
- Queried **Splunk** for event correlation (source IP, user agent, ports)  
- Cross-checked IoCs on **AbuseIPDB** and **VirusTotal**  
- Determined attacker intent (reconnaissance, C2, data exfiltration)

### 4. **Incident Report**
- Created an **Incident Response Report** summarizing:
  - Indicators of Compromise (IoCs)  
  - Timeline of events  
  - Root cause  
  - Mitigation & prevention recommendations

---

## 🧩 Key Findings  
- **Suspicious IP Detected:** 172.16.89.2 communicating on port 80  
- **Attack Type:** Possible Command & Control beacon  
- **Tactics Identified:**  
  - T1049 – System Network Connections Discovery  
  - T1071 – Application Layer Protocol (HTTP)

---

## 🧠 Lessons Learned  
- Gained hands-on experience in analyzing live PCAPs and correlating alerts  
- Improved understanding of MITRE ATT&CK mapping and threat categorization  
- Learned to create structured, evidence-based incident reports

---

## 📊 Visuals & Evidence  
Include screenshots here (e.g., Wireshark filters, Splunk dashboards, Zeek logs):

| Evidence | Description |
|-----------|-------------|
| ![wireshark](https://github.com/Danychr1/The-Cyber-Defense-Playbook-Project/blob/main/Topics/Tools/Labs/Wireshark/Wireshark.md) | Suspicious HTTP request observed |
| ![splunk](images/splunk_search.png) | Event correlation showing repeated beacon attempts |

---

## 🧾 References  
- [MITRE ATT&CK Framework](https://attack.mitre.org/)  
- [Security Onion Documentation](https://securityonionsolutions.com/)  
- [Splunk Security Essentials](https://splunkbase.splunk.com/app/3435/)  

---

## 💬 Author  
**Dany Christel**  
SOC Analyst | DevOps | Cloud Enthusiast  
[GitHub](https://github.com/Danychr1) • [LinkedIn](https://www.linkedin.com/in/dany-christel-qa/) • [Medium](https://medium.com/@christel093)


