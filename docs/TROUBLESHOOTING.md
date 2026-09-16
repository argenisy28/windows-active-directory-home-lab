# Troubleshooting Notes

## 1. Windows Server Installer Repeated Upgrade Prompt

### Symptom
After Windows Server installation restarted, Setup displayed a message stating that an upgrade had been started from installation media.

### Cause
The VM booted from the Windows Server ISO again instead of the newly installed virtual disk.

### Resolution
Removed the ISO from the VirtualBox optical drive and continued booting from the virtual hard disk.

---

## 2. Incorrect Domain Controller Subnet Mask

### Symptom

`ipconfig` showed:

```text
10.10.10.10
255.0.0.0
```

### Cause
The subnet mask had been entered as `/8` instead of `/24`.

### Resolution

Corrected it to:

```text
255.255.255.0
```

and verified connectivity with:

```cmd
ping 10.10.10.1
```

---

## 3. DCDIAG Reported DFSR / SystemLog Failures

### Symptom
`dcdiag` showed failed `DFSREvent` and `SystemLog` tests along with DNS and time-service warnings.

### Investigation
Current AD and DNS functionality was tested separately:

```cmd
nslookup argenislab.test
nslookup -type=SRV _ldap._tcp.dc._msdcs.argenislab.test
dcdiag /test:dns
```

### Result
Current DNS tests passed. Most of the earlier failures were historical warning events generated during initial domain promotion and service startup.

---

## 4. CLIENT01 Received 169.254.x.x Address

### Symptom

```text
Autoconfiguration IPv4 Address: 169.254.x.x
```

The client could not ping `DC01` and DNS queries timed out.

### Cause
DHCP was intentionally disabled on the lab network, but the static client address had not successfully applied.

### Resolution

Configured the interface manually:

```cmd
netsh interface ipv4 set address name="Ethernet" static 10.10.10.20 255.255.255.0 10.10.10.1
netsh interface ipv4 set dnsservers name="Ethernet" static 10.10.10.10 primary
```

Validation:

```cmd
ping 10.10.10.10
nslookup argenislab.test
```

Both succeeded.

---

## 5. Group Policy Did Not Apply

### Symptom

`gpresult /scope computer /r` showed only:

```text
Default Domain Policy
```

The `Client Security Baseline` GPO was missing.

### Cause
`CLIENT01` was still located in Active Directory's default `Computers` container. The custom GPO was linked to the `Lab-Computers` OU.

### Resolution
Moved `CLIENT01` into:

```text
OU=Lab-Computers,DC=argenislab,DC=test
```

Then ran:

```cmd
gpupdate /force
gpresult /scope computer /r
```

The output then showed:

```text
Client Security Baseline
Default Domain Policy
```

After restart, the centrally configured interactive logon message appeared.

---

## 6. Testing Least Privilege / SMB Permissions

### Authorized user
`avelez` is a member of `IT-Support`.

Result:
- Could open `\\DC01\IT`
- Could create and modify files

### Unauthorized user
`tuser` is not a member of `IT-Support`.

Result:
- Windows displayed a network error stating the user did not have permission to access `\\DC01\IT`

This validated group-based authorization at both the SMB share and NTFS layers.
