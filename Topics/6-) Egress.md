# Egress Traffic Analysis with Zeke and Rita/AC Hunter

* Necessity: $Egress traffic analysis is critical for detecting command and control and exfiltration, especially as encryption (SSL/TLS) makes traditional signature-based detection difficult.$
* NetFlow: $A Cisco standard (version 9) for collecting network traffic statistics, useful for analysis engines like SIMs$
* Zeke (formerly Bro): $An industry-standard tool for network traffic analysis known for its speed, large user base, and consistency.$
    * It processes network packet captures or live streams and breaks the data into discrete log files (e.g., DNS, HTTP, FTP, connection logs, SSL/TLS certificate info, SMB, Kerberos).

* Rita (Real Intelligence Threat Analytics):

  * A free tool that processes Zeke logs to identify non-human beaconing traffic, denialist checks, DNS views, and long connection analysis.
  * Beacon Detection: Identifies malicious or non-human beaconing based on consistent connection intervals, tightly clustered data sizes, and quick connection times.
      * Algorithms like K-means clustering and MADMOM (Medium Average Distribution of the Mean) are used to find these consistent clusters.
      
  * Long Connection Analysis: Even if attackers "shatter" connections to appear pseudo-random, Rita can reassemble them to detect if cumulative connection time spans a long duration (e.g., 24 hours).
  * Findings: Rita can detect not only malware beaconing but also legitimate but potentially risky software like TeamViewer, RDP, LAN turtles, and Nuance remote management tools, which act as potential attack vectors.
  * Signal-to-Noise Ratio: Killing ads in an environment significantly reduces network noise and attack vectors, improving detection clarity.
  * Rita is named after the instructor's mother and is committed to remaining free for the community, despite having over 26 associated patents.
 
* AC Hunter: A hosted version of Rita used for labs, allowing users to analyze specific datasets and answer questions about beaconing and fully qualified domain names (FQDNs).
   * Winlab agent example: Showed malware installed but not activated, with 15-second intervals and zero-byte payloads.
   * GCAT example: Malware using gmail.smtp.msa.l.google.com for command and control, exploiting firewalls that ignore Google traffic.
   * DNSCAT example: Malware using DNS as a command and control channel, characterized by a very high FQDN count and lookups from a single system (e.g., 60,000+ FQDNs, 100,000+ total queries).
 
     
* Full PCAP Analysis: Involves setting up capture devices pre-Network Address Translation (NAT) to ensure internal IP addresses are visible. Dedicated capture devices like Coralite and Gigamon are available.

* Advanced Fingerprinting: User agent strings and TLS/SSL negotiation fingerprinting (e.g., JA3/JA4 project) can be used to identify services and traffic types even when data is encrypted.
* Longtail Analysis: Focuses on detecting oddities and anomalies in non-human traffic that fall outside standard updates and patches.
* Rita and AC Hunter can incorporate deny lists from threat intelligence feeds to correlate beaconing traffic with known malicious IPs or hostnames.
* Rita can be installed and run on a Security Onion stack.
