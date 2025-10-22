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
