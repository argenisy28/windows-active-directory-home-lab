# Commands Used

## Windows Networking

Display IP configuration:

```cmd
ipconfig
ipconfig /all
```

Test default gateway:

```cmd
ping 10.10.10.1
```

Test domain controller:

```cmd
ping 10.10.10.10
```

Test external connectivity:

```cmd
ping 8.8.8.8
```

Configure CLIENT01 static IPv4 address:

```cmd
netsh interface ipv4 set address name="Ethernet" static 10.10.10.20 255.255.255.0 10.10.10.1
```

Configure CLIENT01 DNS:

```cmd
netsh interface ipv4 set dnsservers name="Ethernet" static 10.10.10.10 primary
```

## DNS / Active Directory Validation

Resolve the domain:

```cmd
nslookup argenislab.test
```

Verify LDAP SRV records:

```cmd
nslookup -type=SRV _ldap._tcp.dc._msdcs.argenislab.test
```

Domain controller diagnostics:

```cmd
dcdiag
dcdiag /test:dns
```

## Identity / Domain Checks

```cmd
whoami
hostname
echo %USERDOMAIN%
```

## Group Policy

Force policy refresh:

```cmd
gpupdate /force
```

Display computer-scope policy:

```cmd
gpresult /scope computer /r
```

## Active Directory PowerShell

Display domain information:

```powershell
Get-ADDomain
```

Query a user:

```powershell
Get-ADUser avelez
```

Query a group:

```powershell
Get-ADGroup "IT-Support"
```

List group membership:

```powershell
Get-ADGroupMember "IT-Support"
```

Verify computer OU location:

```powershell
Get-ADComputer CLIENT01 -Properties DistinguishedName | Select-Object DistinguishedName
```

## SMB Testing

Open the IT share:

```text
\\DC01\IT
```

Command-line access test:

```cmd
dir \\DC01\IT
```

Review user security groups:

```cmd
whoami /groups
```
