**Room:** [TryHackMe — CTF: Jackpot Overflow](https://tryhackme.com/room/thm-ctf-jackpot-overflow)

_Passwords, cracked hashes, and flags have been omitted from this writeup per TryHackMe's publishing guidelines._

```yaml
title: External RDP Logon From an Untrusted Source Grants Administrative Access
id: fc14f8ac-c4d3-4071-b717-c2b85dc463d2
status: experimental
description: >
  Detects an interactive or unlocked RDP session originating from an
  untrusted source outside Cascadia's approved internal, VPN, and
  vendor network ranges.

author: morgan-reyes

logsource:
  product: windows
  service: security

detection:
  selection:
    EventID: 4624
    LogonType:
      - '7'
      - '10'

  filter_internal_rdp:
    IpAddress|cidr: '10.40.0.0/16'

  filter_vpn_staff:
    IpAddress|cidr: '10.90.0.0/16'
    TargetUserName:
      - r.doyle
      - k.nakamura
      - p.okonkwo

  filter_summitdesk:
    IpAddress|cidr: '198.51.100.0/24'
    TargetUserName: svc_summitdesk_support

  condition: >
    selection and
    not (
      filter_internal_rdp or
      filter_vpn_staff or
      filter_summitdesk
    )
```

- _flag omitted_

## 2nd

```yaml
title: NetScan Enumerates Writable Administrative Shares via a Delete.me Access Test
id: 9ab1b326-e9e3-4b6b-b4c2-81857d774b0b
status: experimental
description: >
  Detects file share object access checks referencing a delete.me marker
  file, consistent with a network scanning tool testing write access
  across discovered administrative shares.

author: morgan-reyes

logsource:
  product: windows
  service: security

detection:
  selection:
    EventID: 5145
    RelativeTargetName|endswith: 'delete.me'

  condition: selection
```

- _flag omitted_

## 3rd

```yaml
title: Remote Access Tool Installed as a Service on Server Infrastructure
id: 6e4f2418-b99a-4df2-868f-fe4086055996
status: experimental
description: >
  Detects a remote access application installed as a Windows service on
  server tier infrastructure, where Cascadia's own helpdesk only ever
  deploys these tools on end user workstations, consistent with POWDER
  WOLF's persistence tradecraft.

author: morgan-reyes

logsource:
  product: windows
  service: system

detection:
  selection:
    EventID: 7045
    ServiceFileName|contains:
      - 'AnyDesk'
      - 'ScreenConnect'
      - 'TeamViewer'

  filter_workstations:
    ComputerName|startswith:
      - 'SNW-PC'
      - 'ALD-PC'
      - 'TBL-PC'

  condition: selection and not filter_workstations
```

- _flag omitted_

## 4th

```yaml
title: 7-Zip Archives Data Directly From a Live Network Share
id: 2a3f3da0-0725-4ef1-a857-05fcf21b0f8c
status: experimental
description: >
  Detects archive creation directly from a UNC network share using
  7-Zip, WinRAR, or PowerShell, consistent with POWDER WOLF's
  collection staging ahead of exfiltration.

author: morgan-reyes

logsource:
  product: windows
  category: process_creation

detection:
  selection_7zip_image:
    Image|endswith:
      - '\7zG.exe'
      - '\7zFM.exe'
      - '\7z.exe'

  selection_7zip_original:
    OriginalFileName:
      - '7z.exe'
      - '7zG.exe'
      - '7zFM.exe'

  selection_winrar:
    OriginalFileName:
      - 'WinRAR.exe'
      - 'Rar.exe'

  selection_powershell:
    Image|endswith:
      - '\powershell.exe'
      - '\pwsh.exe'
    CommandLine|contains: 'Compress-Archive'

  selection_unc:
    CommandLine|re: '\\\\[^\\]+\\[^\\]+'

  filter_monthly_archive:
    ParentImage|endswith: '\wscript.exe'
    CommandLine|contains: 'ReservationsExport_'

  condition: >
    (
      (
        selection_7zip_image
        or selection_7zip_original
        or selection_winrar
        or selection_powershell
      )
      and selection_unc
    )
    and not filter_monthly_archive
```

- _flag omitted_

## 5th

```yaml
title: Lynx Ransomware Payload Executed With Distinctive Encryption Flags
id: fef37376-5b9b-4ada-b67b-2ce4d9177323
status: experimental
description: >
  Detects execution of the Lynx ransomware payload via its distinctive
  command line flags controlling target drive, encryption speed, and
  verbosity, consistent with POWDER WOLF's fleet wide deployment staging.

author: morgan-reyes

logsource:
  product: windows
  category: process_creation

detection:
  selection:
    CommandLine|contains:
      - '--dir'
      - '--mode fast'
      - '--verbose'

  filter_diskoptimizer:
    Image: 'C:\Program Files\ITOpsTools\DiskOptimizer.exe'
    OriginalFileName: 'DiskOptimizer.exe'

  condition: selection and not filter_diskoptimizer
```

- _flag omitted_

# END