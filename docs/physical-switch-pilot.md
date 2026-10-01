# Physical Switch Pilot

The virtual lab validates authentication and authorization logic. The next stage is a controlled physical-switch pilot.

## Phase 1 — Compatibility
Inventory switch models/firmware and confirm 802.1X, RADIUS, VLAN assignment and fallback support.

## Phase 2 — IT bench
Use one managed switch, one non-critical port and one test notebook. Validate boot authentication, reauthentication, reboot/logoff, certificates, NPS/switch logs, RADIUS outage behavior and rollback.

## Phase 3 — Small pilot
Expand to a small number of non-critical corporate endpoints.

## Phase 4 — Exceptions
Design controlled handling for printers, IP phones, POS/embedded and other devices without EAP-TLS.

## Rollback
Maintain a known-good management path outside the pilot. If authentication causes loss of connectivity, remove 802.1X from the pilot port/profile and restore the previous access VLAN/configuration.
