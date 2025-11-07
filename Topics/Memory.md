# Memory Analysis with Volatility
  * Purpose: To detect sophisticated adversaries (e.g., nation-state level) on compromised cloud or virtualized systems without alerting them.
     
  * Tool: Volatility is a leading memory analysis tool used for this purpose.
    
  * Methodology:
      1- Create a memory snapshot of the virtual machine or cloud instance (e.g., in VMware).
    
      2- Analyze the memory dump file offline, ensuring the attacker remains unaware of the investigation.
    
      3- Volatility uses "symbol lookup tables" specific to the operating system version, service pack, and language pack to map memory locations.

<img width="1086" height="674" alt="Volatility Memory Analysis Capabilities" src="https://github.com/user-attachments/assets/018fc589-0f21-44c7-9e1a-49491f98ef59" />


  * Initial Analysis (malfind): A "smoke test" to quickly identify potential malware by looking for memory pages with read, write, and execute (RWE) permissions, which are rare for legitimate processes.
      - Example: trustme.exe was flagged with RWE, but the search UI also showed similar behavior, indicating potential false positives or detection tuning opportunities.
      
  * Network Connections (netscan): Focuses on established, SYN-sent, and closed connections, as Windows often retains connection information in memory longer than expected. 
      - Example: trustme.exe communicating with a Lenode instance (external) and simultaneously making an SMB connection to another internal workstation.
      - A critical observation is that remote SMB shares mounted on another computer system often appear under the system's process (PID 4), making tracing difficult.
      
  * Process Analysis (pslist, pstree): Investigates process IDs, parent process IDs, and process hierarchy to understand execution flow.
      - Example: trustme.exe invokes cmd.exe, which then invokes conhost and net commands, indicating lateral movement.
   
  * DLL List: Examines Dynamic Link Libraries loaded by executables, often revealing patterns seen in other Windows CLI investigations.
