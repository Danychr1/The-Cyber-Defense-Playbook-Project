# Networking Fundamentals: TCP/IP Model
The course focuses on the TCP/IP model over the outdated OSI model, as TCP/IP reflects how modern networks actually work.
The OSI model, created over 35 years ago by consensus, diverged significantly from real-world networking, especially in its session and presentation layers.
Packets are discrete, smaller chunks of a large data stream, each containing headers for transmission.
## IP Header
- The IP header contains routing information for a packet to reach its destination.
- Key components include:
   - Version: Typically IPv4 or IPv6.
   - Source and Destination IP Address: Unique 32-bit numbers identifying the communicating systems, analogous to a computer's phone number.
   - Time to Live (TTL): Limits the number of hops a packet can take.
   - Protocol: Indicates the next-level protocol (e.g., TCP or UDP).
   - Checksum: Ensures packet integrity.
   - Identification, IP Flags, Fragmentation Offset: Used for packet fragmentation, a less common process now due to modern router capabilities.
          - The "evil bit" (an April 1st RFC) was a reserved bit humorously proposed to mark malicious packets.
- Request for Comments (RFCs) are documents that describe how internet technologies and networking protocols are supposed to work, followed by operating systems.

## TCP Header
 - The TCP header provides more detailed communication information, layered beneath the IP header.
 - Key components include:
      - Source and Destination Port: 16-bit numbers (0-65535) identifying specific services or "rooms" on a computer, allowing multiple services to run simultaneously.
      - Sequence and Acknowledgment Numbers: 32-bit pseudo-random numbers used to keep communication streams separate and distinct, especially for large data transfers, incrementing by one for initial setup and then by bytes received.
      - TCP Flags: Indicate the state of a communication stream.
          - SYN (Synchronization): Initiates a connection request.
          - ACK (Acknowledgment): Acknowledges received packets.
          - URG (Urgent): Indicates urgent data.
          - RST (Reset): Resets the connection.
          - FIN (Finish): Terminates the connection.
      - Window Size: Total amount of data an operating system will buffer before flushing to an application, varying by OS.
      - Checksum: A CRC32 checksum for integrity, designed for speed over hyper-accuracy, and used to detect corrupted packets.
  - The TCP 3-way handshake establishes a connection:
    1. Client sends SYN with a sequence number.
    2. Server responds with SYN-ACK, acknowledging the client's sequence number (incremented by one) and sending its own sequence number.
    3. Client responds with ACK, acknowledging the server's sequence number.
 ## UDP Header
 - UDP (User Datagram Protocol) is a "fire and forget" protocol, meaning it sends data as quickly as possible without reliability checks like sequence numbers or acknowledgments.
 - It is suitable for streaming services (e.g., music, video) where dropping a few packets doesn't significantly degrade quality.
 - It contains a source port, destination port, length, and checksum 
