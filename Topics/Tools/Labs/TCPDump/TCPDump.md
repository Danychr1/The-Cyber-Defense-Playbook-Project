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

Result: The file shows Number of packets: 0 and File size: 24 bytes (just the PCAP header).
This means the file is empty! All the existing PCAP files in this directory contain no packet data.
