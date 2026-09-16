# Architecture Overview

## Logical Design

The lab uses one VirtualBox NAT Network named `AD-Lab` with the IPv4 network `10.10.10.0/24`.

```mermaid
flowchart TB
    Internet((Internet))
    VBox["VirtualBox NAT Network\nAD-Lab\n10.10.10.0/24\nGateway 10.10.10.1"]
    DC["DC01\nWindows Server 2025\n10.10.10.10"]
    Client["CLIENT01\nWindows 11 Enterprise\n10.10.10.20"]

    Internet --- VBox
    VBox --- DC
    VBox --- Client

    subgraph DC_Roles["DC01 Roles"]
      ADDS["Active Directory Domain Services"]
      DNS["DNS Server"]
      SMB["SMB File Server"]
      GPO["Group Policy Management"]
    end

    DC --- DC_Roles
```

## Identity Design

Domain:

```text
argenislab.test
```

NetBIOS:

```text
ARGENISLAB
```

Organizational Units:

```text
Lab-Users
Lab-Computers
Lab-Groups
```

Security relationship:

```text
avelez
  └── member of IT-Support
       └── granted Modify/Read access to \\DC01\IT

tuser
  └── not a member of IT-Support
       └── denied access to \\DC01\IT
```

## Group Policy Design

The GPO `Client Security Baseline` is linked to the `Lab-Computers` OU. Because `CLIENT01` is placed in that OU, the computer receives the policy during Group Policy processing.

The policy currently configures an interactive logon title and message to demonstrate centralized policy enforcement.
