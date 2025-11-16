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
<img width="1002" height="424" alt="Pictures 1" src="https://github.com/user-attachments/assets/3fa0d8cc-1df5-4365-9de7-689041ada593" />


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

<img width="1006" height="667" alt="Pictures 2 " src="https://github.com/user-attachments/assets/2cb19aeb-7a0c-43a4-a805-39af0f5bb870" />

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
<img width="993" height="656" alt="Pictures 3" src="https://github.com/user-attachments/assets/7567a597-042c-4f97-86fa-a0aec834cbc7" />

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
<img width="1003" height="553" alt="Pictures 4" src="https://github.com/user-attachments/assets/a22b1d29-2b2d-4392-b2ab-1ce366279d35" />

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

* Purpose: Get overview of all processes at the time of capture
  
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
<img width="995" height="667" alt="Pictures 5" src="https://github.com/user-attachments/assets/e2f81f20-6d0a-4783-9113-c127d07cb469" />

Save output:
```bash
   python3 vol.py -f /root/memory-forensics-lab/memdump.vmem windows.pslist > /root/memory-forensics-lab/02-pslist.txt
```



#### Task 3: View Process Tree (Infection Chain)

See parent-child relationships to trace how malware started:

```bash
   python3 vol.py -f /root/memory-forensics-lab/memdump.vmem windows.pstree

**How to read the tree:**
Explorer.exe (PID: 1234)          ← User's desktop
  └── TrustMe.exe (PID: 5452)     ← User clicked this
      └── cmd.exe (PID: 6789)     ← Malware spawned command prompt
          └── powershell.exe      ← Running malicious commands
```
    
<img width="995" height="340" alt="Pictures 6 " src="https://github.com/user-attachments/assets/a7858fc4-492c-400f-a154-942c15dd9236" />


This tells you:

1. Initial vector: User execution (Explorer.exe parent)
2. Malware: TrustMe.exe
3. Activity: Spawned command shells
4. Technique: Living-off-the-land (using legitimate tools)

Save output:
```bash
   python3 vol.py -f /root/memory-forensics-lab/memdump.vmem windows.pstree > /root/memory-forensics-lab/03-pstree.txt
```
<img width="976" height="89" alt="Pictures 7 " src="https://github.com/user-attachments/assets/4808a447-afc5-430e-8192-1c37ecea38ab" />



#### Task 5: Examine Network Connections
Identify C2 communications and lateral movement:
```bash
   python3 vol.py -f /root/memory-forensics-lab/memdump.vmem windows.netscan
```
<img width="993" height="663" alt="Pictures 8" src="https://github.com/user-attachments/assets/fe14bdb8-23cc-44d3-b856-ccd0363ba553" />


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

<img width="990" height="602" alt="Pictures 9" src="https://github.com/user-attachments/assets/ee13d907-f8d7-41ca-9472-15079eb08ca2" />

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

#### Task 7: Extract Command History
See what commands were executed:
```bash
   python3 vol.py -f /root/memory-forensics-lab/memdump.vmem windows.cmdline
```
<img width="997" height="616" alt="Pictures 10" src="https://github.com/user-attachments/assets/0dc7e50a-db22-48d8-a946-a63e9c3acf43" />

#### Task 8: Search for Suspicious Files
```bash
   python3 vol.py -f /root/memory-forensics-lab/memdump.vmem windows.filescan | grep -i "trustme\|temp\|download"
```
<img width="978" height="584" alt="Pictures 11" src="https://github.com/user-attachments/assets/00d4150c-0ecf-4a43-8338-74df4f015b75" />

This helps locate:
    * Malware file paths
    * Dropped files
    * Staging directories

#### Create Your Investigation Report
Now compile everything into a report:
```
# Create report directory
mkdir -p /root/memory-forensics-lab/report

# Create a summary file
cat > /root/memory-forensics-lab/report/investigation-summary.txt << 'EOF'
MEMORY FORENSICS INVESTIGATION REPORT
======================================

1. SYSTEM INFORMATION
   - OS Version: [From windows.info]
   - Dump Time: [From windows.info]
   - Architecture: [x86/x64]

2. MALWARE IDENTIFIED
   - File Name: TrustMe.exe (example)
   - PID: 5452
   - Parent Process: Explorer.exe (user execution)
   - Location: C:\Users\Victim\Downloads\

3. MALICIOUS ACTIVITY
   - Spawned Processes: cmd.exe, powershell.exe
   - Network Connections: SMB to 10.0.0.50:445
   - DLLs Loaded: ws2_32.dll (networking)
   - Command-line: TrustMe.exe -silent -connect 10.0.0.50

4. INDICATORS OF COMPROMISE (IOCs)
   - File Hash: [If available]
   - Process Name: TrustMe.exe
   - Network IOCs: 10.0.0.50:445
   - Registry Keys: [If found]

5. ATTACK TIMELINE
   1. User downloaded/received TrustMe.exe
   2. User executed from Explorer.exe
   3. Malware spawned cmd.exe
   4. Established SMB connection (lateral movement)
   5. Potential data exfiltration or propagation

6. RECOMMENDATIONS
   - Isolate affected system
   - Check 10.0.0.50 for compromise
   - Block TrustMe.exe at endpoint protection
   - Reset user credentials
   - Review firewall logs for SMB traffic
EOF

# Edit with your findings
nano /root/memory-forensics-lab/report/investigation-summary.txt
```
<img width="1069" height="637" alt="Pictures 12" src="https://github.com/user-attachments/assets/04a2a699-0739-4605-82b2-4a18b766af16" />

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
