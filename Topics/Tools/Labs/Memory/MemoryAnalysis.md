# Memory Forensics Lab: Analyzing a Compromised System 

### Overview

  In this lab, you'll analyze a memory dump from a compromised Windows system using Volatility 3, a powerful open-source forensics tool. You'll learn to identify malicious processes, suspicious network connections, and trace the attack path through process relationships. 
  

### What You'll Learn
* How to use Volatility 3 for memory analysis.
* Identifying suspicious processes and network connections.
* Tracing parent-child process relationships.
* Examining DLLs and command-line arguments.

### Prerequisites
* Kali Linux environment.
* Volatility 3 (version 1.0.0) installed.
* Memory dump file (memdump.vmem)

## Lab Instructions
#### Step 1: Open Terminal and Gain Root Access.

- Open a terminal by clicking the terminal icon in the taskbar or searching for "Terminal" in the Kali menu.
- Elevate to root privileges:
  ```bash
  sudo su -
  ```
#### Step 2: Navigate to Volatility Directory
  ``` bash
cd /opt/volatility3-1.0.0
  ``` 

## Analysis Tasks
#### Task 1: Find Suspicious Memory Pages
  Memory pages with Read, Write, and Execute (RWX) permissions are suspicious because legitimate programs rarely need all three permissions simultaneously. Malware often uses RWX pages to inject and run code.
  
Run the command:
  ``` bash
  python3 vol.py -f ./memdump.vmem windows.malfind.Malfind
  ```
⏱️ Note: This command may take several minutes to complete.

##### What to look for:
* Processes with unusual names (e.g., "TrustMe.exe" - ironic name for malware!).
* Executable code in unexpected memory regions.
* Encoded or obfuscated code patterns.

#### Task 2: Examine Network Connections.
Network connections can reveal command-and-control (C2) communications and lateral movement attempts.
Run the command:
  ``` bash
  python3 vol.py -f ./memdump.vmem windows.netscan
  ```

##### What to look for:
  * Port 445 (SMB): Connections to internal systems may indicate lateral movement.
  * Connections to external IPs on unusual ports.
  * Multiple connections from the same suspicious process
Why this matters: If a compromised system is connecting to other internal machines, the infection may be spreading across your network.

#### Task 3: List Running Processes
Run the command:
  ``` bash
  python3 vol.py -f ./memdump.vmem windows.pslist
  ```

##### What to look for:
  * cmd.exe: Command prompt sessions are unusual for normal users
  * Processes with suspicious names
  * Multiple instances of the same process
  * Processes running from unusual locations (e.g., temp folders, user directories)

Key observation: The presence of cmd.exe without user interaction suggests automated or scripted activity, often a sign of malware.

#### Task 4: View Process Tree
The process tree shows parent-child relationships, helping you trace how malware was executed.
Run the command:
  ``` bash
  python3 vol.py -f ./memdump.vmem windows.pstree
  ```
  **How to read the output:**
  - **Indentation** shows parent-child relationships
  - Follow the chain backward to find the initial infection vector

  **Example analysis:**
  ```
  Explorer.exe (GUI/User interaction)
    └── TrustMe.exe (Suspicious executable)
        └── cmd.exe (Command shell spawned by malware)
  ```

##### What this tells us:

1. User double-clicked TrustMe.exe from Windows Explorer
2. TrustMe.exe spawned a command prompt
3. This command prompt likely executed malicious commands


#### Task 5: Investigate Suspicious Process DLLs
DLLs (Dynamic Link Libraries) loaded by a process can reveal its capabilities and behavior.
Run the command (using PID 5452 for TrustMe.exe):
  ```bash
  python3 vol.py -f ./memdump.vmem windows.dlllist --pid 5452
  ```
##### What to look for:

  * Unusual DLLs: Libraries related to networking, encryption, or system manipulation.
  * DLL paths: Legitimate Windows DLLs load from System32; others may be suspicious
  * Command-line arguments: Shows how the process was started and with what parameters

##### The command-line information reveals:

  * The full path where the executable was run from
  * Any flags or arguments passed to the program
  * Potential evidence of how it was executed (manually, script, scheduled task)

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
