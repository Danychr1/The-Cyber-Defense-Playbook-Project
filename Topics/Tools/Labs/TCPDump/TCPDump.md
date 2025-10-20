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
## Step 3: Stop the Capture

Go back to Terminal 1 and press Ctrl+C to stop the capture.

Verify you captured data:

```bash
capinfos magnitude_live.pcap
ls -lh magnitude_live.pcap
```

You should see the packet count is greater than 0 and file size is larger than 24 bytes.

## Basic TCPDump Filters
Now that we have actual packet data, let's analyze it!

### Filter 1: View All Traffic from a Specific Host

```bash
tcpdump -n -r magnitude_live.pcap host 172.16.89.2
```

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

