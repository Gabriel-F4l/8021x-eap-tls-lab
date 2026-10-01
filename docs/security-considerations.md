# Security Considerations

EAP-TLS should validate both endpoint and RADIUS server certificates. Restrict the trusted CA and expected RADIUS server name where possible.

## Never commit
- RADIUS shared secrets
- Private keys
- PFX/P12 files
- Credentials
- Production certificates
- Sensitive production network exports

## Production design
Evaluate NPS redundancy, CRL/OCSP, auto-enrollment/renewal, RADIUS outage behavior, break-glass access, MAB exceptions, quarantine VLANs, logging and alerting.
