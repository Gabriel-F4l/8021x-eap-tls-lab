# 🔐 802.1X EAP-TLS Network Access Control Lab

Proof of Concept for certificate-based wired network access control using **IEEE 802.1X**, **EAP-TLS**, **Active Directory**, **AD CS**, **NPS/RADIUS**, **Group Policy** and a virtual Linux authenticator.

## 🎯 Objective
Validate machine-certificate authentication and centralized authorization.

- ✅ Authorized computer → **NPS Event ID 6272**
- ❌ Computer removed from authorization group → **NPS Event ID 6273**
- ✅ Computer added back → access restored

> This repository documents an isolated lab. It does not represent any production environment or employer infrastructure.

## 🏗️ Architecture

```text
LAB-W11 (Windows 11 / Supplicant / 192.168.10.111)
        │ EAPOL / IEEE 802.1X
        ▼
LAB-SW01 (Ubuntu + hostapd wired / 192.168.10.1)
        │ RADIUS UDP 1812/1813
        ▼
LABSR01 (Windows Server 2022 / 192.168.10.112)
AD DS + DNS + AD CS + NPS
        │
        ▼
GG-8021X-EAPTLS (AD authorization group)
```

## 🧩 Components

| Component | Role |
|---|---|
| Windows Server 2022 | AD DS, DNS, AD CS and NPS |
| Windows 11 Pro | 802.1X supplicant |
| Ubuntu Linux | Virtual wired authenticator |
| AD CS | Machine and RADIUS certificates |
| NPS | RADIUS authentication/authorization |
| Group Policy | Wired 802.1X configuration |
| hostapd | EAPOL ↔ RADIUS authenticator |

## 🔄 Authentication flow
1. Windows Wired AutoConfig starts 802.1X.
2. LAB-W11 sends EAPOL to LAB-SW01.
3. hostapd forwards authentication to NPS over RADIUS.
4. The client validates the RADIUS server certificate.
5. LAB-W11 presents its machine certificate.
6. NPS validates certificate and machine identity.
7. NPS evaluates membership in `GG-8021X-EAPTLS`.
8. Authorized endpoints receive access; unauthorized endpoints are denied.

## 🔑 EAP-TLS policy
- IEEE 802.1X: Enabled
- EAP: **Microsoft: Smart Card or other certificate (EAP-TLS)**
- Authentication mode: **Computer only**
- Machine certificate: automatic selection
- Server certificate validation: enabled
- Trusted lab CA: `eaptls-LABSR01-CA`
- Expected RADIUS server: `LABSR01.eaptls.lab`

## 🧪 Validation

### Authorized endpoint
hostapd:
```text
AUTH_PAE entering state AUTHENTICATED
IEEE 802.1X: authorizing port
authenticated - EAP type: 13 (TLS)
```
NPS: **Event ID 6272 — Network Policy Server granted access**

### Unauthorized endpoint
LAB-W11 was temporarily removed from `GG-8021X-EAPTLS` and reauthenticated.

NPS: **Event ID 6273 — Network Policy Server denied access**

### Restoration
LAB-W11 was added back to the group and reauthenticated. NPS generated a new **6272**.

## 🔎 Troubleshooting
The initial Windows wired profile used **PEAP** while NPS required **EAP-TLS**. A Group Policy wired profile corrected the method to Smart Card or other certificate / Computer only.

Computer GPO also initially failed because the endpoint could not correctly locate the Domain Controller through DNS. After correcting DC/DNS reachability:
```powershell
nltest /dsgetdc:eaptls.lab
gpupdate /force
```
the GPO applied and the client profile changed to EAP-TLS.

## 📚 Documentation
- [Implementation](docs/implementation.md)
- [Troubleshooting](docs/troubleshooting.md)
- [Security considerations](docs/security-considerations.md)
- [Physical switch pilot](docs/physical-switch-pilot.md)
- [hostapd example](configs/hostapd-wired.example.conf)

## ⚠️ Lab limitations
hostapd with the wired driver acts as a virtual authenticator. The PoC validates the EAPOL → RADIUS → EAP-TLS authentication and authorization path, but it does **not** replace validation of physical switch port enforcement.

A production pilot should validate managed-switch 802.1X behavior, RADIUS attributes, VLANs, MAB/fallback, NPS redundancy, certificate lifecycle, fail-open/fail-close behavior, monitoring and rollback.

## 🔒 Security note
Secrets, private keys and RADIUS shared secrets are intentionally excluded from this repository.

---
**Topics:** `802.1X` · `EAP-TLS` · `RADIUS` · `NPS` · `Active Directory` · `AD CS` · `PKI` · `Group Policy` · `Network Security`
