# DeepBlueCLI 

DeepBlueCLI is a free PowerShell tool from Eric Conrad that helps us find sneaky activity in Windows event logs. It’s a great way to see how UEBA-style detections (behavioral patterns, not just signatures) can surface attacks that slip past traditional defenses.

Follow these quick steps:
**Note:**  
Setup Steps

- Download the project from GitHub ([SANS-Blue-Team/DeepBlueCLI](https://github.com/sans-blue-team/DeepBlueCLI.git))
- Extract the files to a directory
- No installation needed - it's a standalone PowerShell script
- We have to download
  
## 1. Open **Windows PowerShell**.
   - Run as administrator.

## 2. Change into the lab folder:

```powershell
cd .\DeepBlueCLI-master
Get-ChildItem
```
<img width="1358" height="877" alt="powershell 1 " src="https://github.com/user-attachments/assets/30999d31-f6dd-4f4a-a227-d6ce1fc60d4b" />

## 3. Allow scripts to run:

```powershell
Set-ExecutionPolicy Unrestricted
```
You’ll likely be asked to confirm — type **Y** and press Enter.

<img width="1365" height="256" alt="powershell 2" src="https://github.com/user-attachments/assets/b7f7de96-0375-4d1d-a823-2cbde22b1c0f" />


### What to look for

Attackers often create extra user accounts on systems they’ve compromised. Why? Because malware is noisy and easier to detect, a normal-looking user account gives them persistence and helps them blend in. DeepBlueCLI makes that behavior obvious in the logs.

To check for new-user creation in a sample event file:

```powershell
.\DeepBlue.ps1 .\evtx\new-user-security.evtx
```
You’ll see results highlighting account-creation events to investigate.

<img width="1411" height="813" alt="powershell 3" src="https://github.com/user-attachments/assets/187bae67-29b5-4db8-a311-f5a31f9ff292" />


### Password spraying

Password spraying is a stealthy attack where an attacker tries one common password (e.g., `Summer2020`) across many accounts. It often avoids lockouts and can slip past SIEM rules that focus on single-account brute force. This is exactly the kind of pattern UEBA should catch.

Try these logs to see password-spray detection in action:

```powershell
.\DeepBlue.ps1 .\evtx\smb-password-guessing-security.evtx
```
<img width="1407" height="904" alt="powershell 4" src="https://github.com/user-attachments/assets/ef717e7d-cb85-4fdd-bd8b-6a6fe8aec4bb" />


```powershell
.\DeepBlue.ps1 .\evtx\password-spray.evtx
```
<img width="1413" height="863" alt="powershell 5" src="https://github.com/user-attachments/assets/ac28cd0a-e50f-497b-a6bf-b60a08fddd99" />

### Obfuscation and encoding

Attackers also hide malicious commands using encoding or obfuscation to evade signature detection. Those techniques are uncommon in legitimate scripts, so they’re strong indicators of malicious activity.

Check an example of obfuscation detection:

```powershell
.\DeepBlue.ps1 .\evtx\Powershell-Invoke-Obfuscation-encoding-menu.evtx
```
<img width="1416" height="914" alt="powershell 6" src="https://github.com/user-attachments/assets/fa9d3823-6d83-4a12-a581-357cc9ecc8fc" />

---

DeepBlueCLI gives us quick, behavior-focused signals — new accounts, repeated failed logins, and encoded commands — so we can triage faster and dig deeper where it matters. Want a short lab file or expected output snapshots to go with these steps?
