# Memory Forensics Lab: Analyzing a Compromised System 

### Overview

  In this lab, you'll analyze a memory dump from a compromised Windows system using Volatility 3, a powerful open-source forensics tool. You'll learn to identify malicious processes, suspicious network connections, and trace the attack path through process relationships. 
  

### What You'll Learn
* How to use Volatility 3 for memory analysis.
* Identifying suspicious processes and network connections.
* Tracing parent-child process relationships.
* Examining DLLs and command-line arguments.
* Understand parent-child process relationships
* Extract DLLs and command-line arguments
* Create forensic investigation reports

### Lab Scenario
You're investigating a compromised Windows system. A memory dump was captured during the incident. Your job is to:
1. Identify the malware
2. Determine how it was executed
3. Find what it communicated with
4. Trace the infection chain
5. Document indicators of compromise (IOCs)

### Prerequisites
Required

✅ Kali Linux (VM or physical)

✅ 2GB+ free disk space

✅ Internet connection

✅ Root/sudo access

### Optional but Helpful

📝 Note-taking application
🖥️ Two monitors (one for instructions, one for terminal)
📚 Basic Linux command-line knowledge
⏱️ Note: This command may take several minutes to complete.

## Lab Instructions
### Part 1: Environment Setup
#### Step 1: Open Terminal and Gain Root Access.

- Open a terminal by clicking the terminal icon in the taskbar or searching for "Terminal" in the Kali menu.
- Elevate to root privileges:
  ```bash
  sudo su -
  ```

#### Step 2: Update System (Optional but Recommended)
  ``` bash
  # Update package lists
  apt update

  # Optional: Upgrade packages (takes time)
  # apt upgrade -y
  ```

#### Step 3: Install Volatility 3
3.1 Install Prerequisites
  ```bash
     apt install -y python3 python3-pip git
  ```

3.2 Clone Volatility 3 from GitHub
  ```bash
     cd /opt
     git clone https://github.com/volatilityfoundation/volatility3.git
  ```

3.3 Navigate to Directory
  ```bash 
     cd volatility3
  ```

3.4 Install Python Dependencies
  ```bash
     pip3 install -r requirements.txt
  ```
  Note: This may take 2-3 minutes.
  
3.5 Verify Installation

  ``` bash
     python3 vol.py -h
  ```
Expected output: You should see Volatility 3 help menu with available commands.

3.6 Create Convenient Alias (Optional)
  ``` bash
    echo "alias volatility3='python3 /opt/volatility3/vol.py'" >> /root/.bashrc
    source /root/.bashrc
  ```

Now you can use volatility3 instead of typing the full path

#### Step 4: Create Lab Working Directory
  ```bash
     mkdir -p /root/memory-forensics-lab
     cd /root/memory-forensics-lab
  ```

#### Step 5: Verify Setup
  ```bash
    # Check Volatility location
    ls -la /opt/volatility3/vol.py

    # Check working directory
    pwd

    # Test Volatility works
    cd /opt/volatility3
    python3 vol.py --help | head -20
  ```

✅ Checkpoint: You should see Volatility help output with no errors

### Part 2: Obtain Memory Dump
  - Use VirtualBox Shared Folders
    1. Configure VirtualBox

       * Devices → Shared Folders → Shared Folders Settings
       * Click folder icon with +
       * Select Downloads folder
       * Check Auto-mount.
       * Click OK

    2. Access on Kali

```bash
   # Install Guest Additions
   apt install -y virtualbox-guest-utils virtualbox-guest-x11

   # Create mount point
   mkdir -p /mnt/shared

   # Mount (replace 'Downloads' with your shared folder name)
   sudo mount -t vboxsf Downloads /mnt/shared

   # Copy file
   sudo cp /mnt/shared/MemLabs-Lab1.7z /root/memory-forensics-lab/
```

Step 6: Extract Memory Dump
6.1 Install Extraction Tools

```bash
   apt install -y p7zip-full unzip
```

6.2 Extract the Archive
```bash
   cd /root/memory-forensics-lab

  # If it's a .7z file
  7z x MemLabs-Lab1.7z

  # If it's a .zip file
  unzip MemLabs-Lab1.zip
```

✅ Checkpoint: You should have /root/memory-forensics-lab/memdump.vmem with size > 100MB

### Part 3: Memory Analysis Tasks

#### Overview of Analysis Workflow

``` 
1. System Info → Identify OS and architecture
2. Process List → Find running processes
3. Process Tree → Trace parent-child relationships
4. Malfind → Detect injected code
5. Network Scan → Find connections
6. DLL Analysis → Examine loaded libraries
7. Command Line → See execution arguments
``` 
#### Task 1: Identify System Information
* Purpose: Understand the compromised system's configuration

```bash
   cd /opt/volatility3
   python3 vol.py -f /root/memory-forensics-lab/memdump.vmem windows.info
``` 

What to document:

* Windows version (7, 8, 10, 11)
* Architecture (x86 or x64)
* Number of processors
* System time when dump was captured

Save output:
```bash
   python3 vol.py -f /root/memory-forensics-lab/memdump.vmem windows.info > /root/memory-forensics-lab/01-info.txt
``` 

#### Task 2: List All Running Processes

* Purpose: Get overview of all processes at time of capture
  
```bash
   python3 vol.py -f /root/memory-forensics-lab/memdump.vmem windows.pslist
```
| **Process**        | **Normal?** | **Red Flag**                   | **Notes**                                   |
| ------------------ | ----------- | ------------------------------ | ------------------------------------------- |
| **explorer.exe**   | ✅ Yes       | —                              | Multiple instances can be suspicious.       |
| **cmd.exe**        | ⚠️ Maybe    | Unexpected for normal users    | Often launched by scripts or attackers.     |
| **powershell.exe** | ⚠️ Maybe    | Often used by malware          | Check for encoded or hidden commands.       |
| **svchost.exe**    | ✅ Yes       | Wrong path (not in *System32*) | Each instance should serve a valid service. |
| **TrustMe.exe**    | ❌ No        | Suspicious name                | Unknown or untrusted executable.            |
| **random.exe**     | ❌ No        | Random character names         | Common sign of malware obfuscation.         |


Example: PID 5452 = TrustMe.exe

Save output:
```bash
   python3 vol.py -f /root/memory-forensics-lab/memdump.vmem windows.pslist > /root/memory-forensics-lab/02-pslist.txt
``` 
#### Task 3: View Process Tree (Infection Chain)

See parent-child relationships to trace how malware started:

```bash
   python3 vol.py -f /root/memory-forensics-lab/memdump.vmem windows.pstree

**How to read the tree:**
```
Explorer.exe (PID: 1234)          ← User's desktop
  └── TrustMe.exe (PID: 5452)     ← User clicked this
      └── cmd.exe (PID: 6789)     ← Malware spawned command prompt
          └── powershell.exe      ← Running malicious commands
          
This tells you:

1. Initial vector: User execution (Explorer.exe parent)
2. Malware: TrustMe.exe
3. Activity: Spawned command shells
4. Technique: Living-off-the-land (using legitimate tools)

Save output:
```bash
   python3 vol.py -f /root/memory-forensics-lab/memdump.vmem windows.pstree > /root/memory-forensics-lab/03-pstree.txt
```
#### Task 5: Examine Network Connections
Identify C2 communications and lateral movement:
```bash
   python3 vol.py -f /root/memory-forensics-lab/memdump.vmem windows.netscan
```
**What to look for:**

| Port | Protocol | Suspicious If... |
|------|----------|------------------|
| 445 | SMB | Connecting to multiple internal IPs |
| 80/443 | HTTP/HTTPS | Unusual external IPs |
| 4444 | Metasploit | Common C2 port |
| 8080 | HTTP-Alt | Proxy or C2 communication |

**Example suspicious connection:**
```
Proto  LocalAddr           ForeignAddr         State       PID    Owner
TCPv4  192.168.1.100:49152  10.0.0.50:445      ESTABLISHED 5452   TrustMe.exe
```
What this means:
  * Port 445 (SMB) = File sharing protocol
  * Internal IP (10.0.0.50) = Another computer on network
  * TrustMe.exe = Malware trying to spread

Save output:
```bash
   python3 vol.py -f /root/memory-forensics-lab/memdump.vmem windows.netscan > /root/memory-forensics-lab/04-netscan.txt
``` 
#### Task 6: Investigate Suspicious Process DLLs

Pick a suspicious PID from Task 3 (e.g., 5452 for TrustMe.exe):
``` 
bash
# Replace 5452 with YOUR suspicious PID
python3 vol.py -f /root/memory-forensics-lab/memdump.vmem windows.dlllist --pid 5452
```
**What to look for:**

**Networking capabilities:**
- ws2_32.dll = Sockets/networking
- wininet.dll = Internet functions
- urlmon.dll = URL downloads

**Encryption:**
- bcrypt.dll = Cryptography
- crypt32.dll = Certificates/crypto

**Suspicious paths:**
- DLLs NOT from C:\Windows\System32\
- DLLs from Temp folders
- DLLs from user directories

**Command-line reveals:**
```
Command line: C:\Users\Victim\Downloads\TrustMe.exe -silent -connect 10.0.0.50
``` 
This shows:

* Where it was run from (Downloads folder)
* How it was run (with flags)
* What it was doing (connecting to 10.0.0.50)

Save output:
```bash
   python3 vol.py -f /root/memory-forensics-lab/memdump.vmem windows.dlllist --pid 5452 > /root/memory-forensics-lab/05-dlllist.txt
```

Task 7: Extract Command History
See what commands were executed:
```bash
   python3 vol.py -f /root/memory-forensics-lab/memdump.vmem windows.cmdline
```

Task 8: Search for Suspicious Files
```bash
   python3 vol.py -f /root/memory-forensics-lab/memdump.vmem windows.filescan | grep -i "trustme\|temp\|download"
``` 
This helps locate:
    * Malware file paths
    * Dropped files
    * Staging directories

##### Tips for Success
✅ Take notes as you run each command.

✅ Look for patterns across different analysis types.

✅ Cross-reference findings (match PIDs between commands).

✅ Be patient - some commands take time on large memory dumps.

✅ Think like an attacker - what would they do next?

#### Continuous with Labs? 
- [Next Lab]()

#### Would you like to go back to the previous Lab?
- [Previous Lab](https://github.com/Danychr1/The-Cyber-Defense-Playbook-Project/blob/main/Topics/Tools/Labs/Firewall/Firewall.md)
