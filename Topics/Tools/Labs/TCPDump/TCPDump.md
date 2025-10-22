# TCPDump Lab - Network Traffic Analysis Basics
In this lab, we will explore essential TCPDump filters that every SOC and security analyst should master.

## Lab Setup
Let's get started by opening a Terminal on Kali-Linux Virtual Machine.
First, elevate to root:

```bash
sudo su -
```
Navigate to the working directory:

```bash
cd /opt/tcpdump
```
## Understanding the Problem: Empty PCAP Files
Before we begin analysis, we need to understand a critical issue. Let's check our PCAP file:

```bash
capinfos magnitude_1hr.pcap
```

- Result: The file shows Number of packets: 0 and File size: 24 bytes (just the PCAP header).
- This means the file is empty! All the existing PCAP files in this directory contain no packet data.

<img width="783" height="600" alt="Screenshot 2025-10-21 at 1 02 05 AM" src="https://github.com/user-attachments/assets/5d5eb923-5880-4b0d-81ed-d37b3477b3b0" />


## Creating a Live Capture
Since we don't have pre-existing data, we'll capture live traffic for analysis. Based on our network reconnaissance, we know that 172.16.89.2 is an active host on our network (responds to ping, TTL=128 indicates Windows).

## Step 1: Start the Packet Capture
Open your first terminal and start capturing:

```bash
tcpdump -i any -w magnitude_live.pcap
```
### Leave this running!
## Step 2: Generate Network Traffic
Open a second terminal and generate various types of traffic:

```bash
# Generate ICMP traffic (ping)
ping -c 50 172.16.89.2

# Attempt web traffic
curl http://172.16.89.2

# Try common ports
nmap -p 80,443,445,3389,22 172.16.89.2

# Try SMB enumeration (Windows file sharing)
smbclient -L //172.16.89.2 -N 2>/dev/null

```
<img width="1520" height="841" alt="Screenshot 2025-10-21 at 1 10 14 AM" src="https://github.com/user-attachments/assets/e9585ad9-c8f3-48ed-93f9-548558d1e2cf" />


## Step 3: Stop the Capture

Go back to Terminal 1 and press Ctrl+C to stop the capture.

Verify you captured data:

```bash
capinfos magnitude_live.pcap
ls -lh magnitude_live.pcap
```
<img width="785" height="595" alt="Screenshot 2025-10-21 at 1 12 40 AM" src="https://github.com/user-attachments/assets/6eac764e-4e71-4cd3-81b2-2473124ffbfa" />


You should see the packet count is greater than 0 and file size is larger than 24 bytes.

## Basic TCPDump Filters
Now that we have actual packet data, let's analyze it!

### Filter 1: View All Traffic from a Specific Host

```bash
tcpdump -n -r magnitude_live.pcap host 172.16.89.2
```
<img width="788" height="596" alt="Screenshot 2025-10-21 at 1 16 31 AM" src="https://github.com/user-attachments/assets/1d6fef70-facf-45c3-877d-4dfd3b678ee7" />


### What this shows:

* -n = Do not resolve hostnames (faster, shows raw IPs)
* -r = Read from a file

### Packet anatomy displayed:

* Timestamp: When the packet was captured
* Protocol: TCP, UDP, ICMP, etc.
* Source IP + Port → Destination IP + Port
* Flags: TCP control bits (SYN, ACK, PSH, FIN, etc.)
* Sequence numbers: TCP sequencing information
* Data size: Packet length

### Filter 2: Host + Port Combination

Let's get more specific by adding a port filter:

```bash
tcpdump -n -r magnitude_live.pcap host 172.16.89.2 and port 445
```
This shows only SMB (Windows file sharing) traffic on port 445.

### Try other common ports:

```bash
# DNS traffic
tcpdump -n -r magnitude_live.pcap host 172.16.89.2 and port 53

# RDP traffic  
tcpdump -n -r magnitude_live.pcap host 172.16.89.2 and port 3389
```

### Filter 3: View Packet Payload (ASCII)

See the actual content of packets with the -A flag:

```bash
bashtcpdump -n -r magnitude_live.pcap host 172.16.89.2 and port 445 -A
```
This displays:

- All metadata (as before)
- ASCII-decoded packet payload
- Useful for reading HTTP requests, DNS queries, plaintext protocols

### Filter 4: View Packet Payload (Hex + ASCII)

For a complete view, add the -X flag:

```bash
bashtcpdump -n -r magnitude_live.pcap host 172.16.89.2 -AX | less
```
<img width="778" height="601" alt="Screenshot 2025-10-21 at 1 53 16 AM" src="https://github.com/user-attachments/assets/d1f52684-aa24-4a32-86c1-f41e97d8990d" />

This shows:
- Hex dump on the left
- ASCII interpretation on the right
- Perfect for analyzing binary protocols or encoded data

### Filter 5: ICMP Traffic Only (Ping)

Since we generated lots of ping traffic:

```bash
bashtcpdump -n -r magnitude_live.pcap icmp
```

This isolates ping requests and replies, useful for:

- Network connectivity testing
- Detecting ICMP tunneling
- Identifying host discovery scans

Filter 6: Network Range

View traffic to/from an entire subnet:

```bash
bashtcpdump -n -r magnitude_live.pcap net 172.16.89.0/24
```
<img width="818" height="627" alt="Screenshot 2025-10-21 at 1 58 23 AM" src="https://github.com/user-attachments/assets/439faecd-8025-4f93-908e-ae862c86bd39" />

Why this matters:

- Detect lateral movement across multiple hosts
- Identify command & control (C2) servers using multiple IPs
- Spot network scanning activity

Filter 7: Protocol-Specific

View specific protocols:
```bash
# IPv6 traffic
tcpdump -n -r magnitude_live.pcap ip6

# TCP only
tcpdump -n -r magnitude_live.pcap tcp

# UDP only
tcpdump -n -r magnitude_live.pcap udp
```
## Additional Resources
Comprehensive TCPDump Guide:
https://danielmiessler.com/study/tcpdump/

## Lab Checklist

  ✅ Created live capture with traffic to 172.16.89.2
 
  ✅ Filtered by host.
 
  ✅ Filtered by host + port.
 
  ✅ Viewed ASCII payload (-A)
 
  ✅ Viewed hex dump (-X)
  
  ✅ Filtered by protocol (ICMP, TCP, UDP)
  
  ✅ Filtered by network range

   ## 👨🏽‍💻 Happy hunting! 🔍
