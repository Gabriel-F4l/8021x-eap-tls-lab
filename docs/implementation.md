# Implementation

## Roles
- **LABSR01** — Windows Server 2022: AD DS, DNS, AD CS and NPS.
- **LAB-W11** — Windows 11 Pro domain-joined supplicant.
- **LAB-SW01** — Ubuntu host running hostapd wired.

## Active Directory
Use the security group `GG-8021X-EAPTLS` as the authorization condition.

## PKI
The workstation uses a machine certificate with a private key and Client Authentication capability. NPS uses a server certificate trusted by the workstation.

## NPS
Configure LAB-SW01 as a RADIUS client. Create a Grant Access Network Policy requiring `GG-8021X-EAPTLS` and **Smart Card or other certificate (EAP-TLS)**.

## Group Policy
```text
Computer Configuration
└── Policies
    └── Windows Settings
        └── Security Settings
            └── Wired Network (IEEE 802.3) Policies
```
Deploy EAP-TLS with **Computer only** authentication and server-certificate validation.

## Validation
```powershell
nltest /dsgetdc:eaptls.lab
gpupdate /force
netsh lan show profiles
netsh lan reconnect interface="Ethernet1"
```

Authenticator debug:
```bash
sudo hostapd -dd /etc/hostapd/hostapd-wired.conf
```

Validate NPS Event IDs **6272** and **6273**.
