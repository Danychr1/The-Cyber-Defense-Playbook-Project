# Deep Blue CLI for Triage and Log Analysis

* Deep Blue CLI is a tool that automates the process of analyzing event logs, serving as a user entity behavioral analytics (UEBA) tool for triage in potentially compromised environments.

* It can be run against event logs from a domain controller or a suspected compromised system (with proper logging enabled, like command line, PowerShell, or Sysmon).

* The tool helps identify "shady behaviors" such as password spraying, password guessing, new user creation, and various encoding techniques used by attackers.

* Deep White CLI is an evolution that incorporates Sysmon event logs for more robust visibility into Windows systems.

* A practical lab involves using PowerShell to set an unrestricted execution policy and running Deep Blue CLI against built-in event logs.

* An example of an alert might show a user account, like Bertha Schultz, attempting rapid authentication to multiple accounts.

* Attackers may use tricks like changing usernames to a dash or creating users with specific SIDs (e.g., Internet user account, SID 1000) and adding them to administrator groups to hide their presence.
