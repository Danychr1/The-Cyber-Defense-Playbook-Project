# DeepBlueCLI 

DeepBlueCLI is a free PowerShell tool from Eric Conrad that helps us find sneaky activity in Windows event logs. It’s a great way to see how UEBA-style detections (behavioral patterns, not just signatures) can surface attacks that slip past traditional defenses.

Follow these quick steps:
**Note:**  
Setup Steps

- Download the project from GitHub ([SANS-Blue-Team/DeepBlueCLI](https://github.com/sans-blue-team/DeepBlueCLI.git))
- Extract the files to a directory
- No installation needed - it's a standalone PowerShell script
- we have to download
  
1. Open **Windows PowerShell**.

2. Change into the lab folder:

```powershell
cd \IntroLabs
cd .\DeepBlueCLI-master
```

3. Allow scripts to run:

```powershell
Set-ExecutionPolicy Unrestricted
```

You’ll likely be asked to confirm — type **Y** and press Enter.

### What to look for

Attackers often create extra user accounts on systems they’ve compromised. Why? Because malware is noisy and easier to detect, a normal-looking user account gives them persistence and helps them blend in. DeepBlueCLI makes that behavior obvious in the logs.

To check for new-user creation in a sample event file:

```powershell
.\DeepBlue.ps1 .\evtx\new-user-security.evtx
```

You’ll see results highlighting account-creation events to investigate.

### Password spraying

Password spraying is a stealthy attack where an attacker tries one common password (e.g., `Summer2020`) across many accounts. It often avoids lockouts and can slip past SIEM rules that focus on single-account brute force. This is exactly the kind of pattern UEBA should catch.

Try these logs to see password-spray detection in action:

```powershell
.\DeepBlue.ps1 .\evtx\smb-password-guessing-security.evtx
.\DeepBlue.ps1 .\evtx\password-spray.evtx
```

### Obfuscation and encoding

Attackers also hide malicious commands using encoding or obfuscation to evade signature detection. Those techniques are uncommon in legitimate scripts, so they’re strong indicators of malicious activity.

Check an example of obfuscation detection:

```powershell
.\DeepBlue.ps1 .\evtx\Powershell-Invoke-Obfuscation-encoding-menu.evtx
```

---

DeepBlueCLI gives us quick, behavior-focused signals — new accounts, repeated failed logins, and encoded commands — so we can triage faster and dig deeper where it matters. Want a short lab file or expected output snapshots to go with these steps?
