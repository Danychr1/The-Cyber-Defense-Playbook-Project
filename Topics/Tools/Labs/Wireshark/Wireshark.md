# 📡 Wireshark Capture & Protocol Analysis Lab

📝 Overview
This project captures and analyzes live network traffic using Wireshark on Windows.
The goal was to capture key network protocols and demonstrate packet analysis using Wireshark display filters.

Now that we have spent a little time working with tcpdump, let's take a look at Wireshark.

We want to make it clear that Wireshark is not "better" than tcpdump. They each have very strong pros and cons.

## Pros of tcpdump
  * tcpdump is fast and very lightweight.
  * It is scriptable.

## Cons of tcpdump

  * It is contained within the terminal, and to be honest, having some visualizations can be very, very helpful when dealing with large datasets.

## Pros of Wireshark

  * Great visual representation of networking packets and more.

## Cons of Wireshark

  * Frequently gives up and freezes on large files.

Basically, it is key to learn and know both.

Let's get started.

## 📦 Traffic Types Captured

  - ICMP — Captured via ping google.com
  - DNS — Captured via nslookup openai.com
  - TCP 3-Way Handshake — Captured via a fresh web connection
  - TCP Port 80 Traffic (HTTP-related) — Captured despite modern HTTPS enforcement

## 📸 Screenshots

### 1. ICMP (Ping Traffic)
Captured Echo Request and Echo Reply packets.

<img width="1285" height="601" alt="icmp (Ping Traffic) " src="https://github.com/user-attachments/assets/b7242020-0243-4207-ad8c-2e654cf49148" />


### 2. DNS (Domain Name System Lookup)
Captured DNS query and response during nslookup.

<img width="1218" height="745" alt="DNS (Domain Name System Lookup)" src="https://github.com/user-attachments/assets/338bcdb9-7022-4791-8a9c-ccf0beb7de77" />


### 3. TCP 3-Way Handshake
Captured the SYN → SYN-ACK → ACK sequence during a connection setup.
<img width="1284" height="751" alt="TCP 3-Way Handshake" src="https://github.com/user-attachments/assets/8d95cb69-44c2-4e03-9a8c-702450bdf299" />


### 4. TCP Port 80 Traffic (HTTP Attempt)
Captured TCP packets directed to Port 80 (HTTP protocol port), even though direct HTTP traffic was force-upgraded to HTTPS.

<img width="1278" height="404" alt="TCP Port 80 Traffic (HTTP Attempt)" src="https://github.com/user-attachments/assets/2ce0f732-55c9-4274-897a-032b37c14f0b" />

## 🧠 Skills Practiced
 - Packet capturing and saving
 - Applying Wireshark filters
 - Analyzing ICMP, DNS, TCP handshakes, and HTTP-related traffic
 - Documenting findings with screenshots
 - Real-world troubleshooting with HTTPS-only environments

## 🛡 Notes
Due to modern network and browser security policies, direct HTTP traffic could not be captured as it was auto-upgraded to HTTPS. TCP Port 80 traffic was still captured and analyzed as part of the lab.

## Continuing the course?
Next Lab

## Want to go back?

- [Previous Lab](https://github.com/Danychr1/The-Cyber-Defense-Playbook-Project/blob/main/Topics/Tools/Labs/TCPDump/TCPDump.md)

  
