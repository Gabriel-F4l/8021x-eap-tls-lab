# Troubleshooting

## PEAP vs EAP-TLS mismatch
**Symptom:** EAP authentication failed despite a valid machine certificate.

**Finding:** Windows wired profile used PEAP while NPS required EAP-TLS.

**Fix:** deploy a GPO wired profile using **Smart Card or other certificate** and **Computer only**.

## Computer GPO did not apply
**Symptom:** `gpupdate /force` failed for Computer Policy.

**Finding:** the endpoint could not locate the Domain Controller through the configured DNS path.

**Validation:**
```powershell
nslookup LABSR01.eaptls.lab
nltest /dsgetdc:eaptls.lab
```
After correcting DNS/DC discovery, Group Policy applied successfully.

## Authorization validation
Removing LAB-W11 from `GG-8021X-EAPTLS` produced NPS Event ID **6273**. Adding it back and reauthenticating produced **6272** again.
