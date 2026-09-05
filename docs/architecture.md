# Architecture and threat model

## Design objective

The homelab provides continuously available household services while creating
a controlled environment for infrastructure and defensive-security learning.
Ubuntu runs directly on the hardware. Long-running applications use Docker,
while higher-risk learning workloads are planned for isolated virtual machines.

## Trust boundaries

| Boundary | Risk | Mitigation |
| --- | --- | --- |
| Internet to home network | Unsolicited access | Avoid exposing management interfaces publicly |
| Remote device to server | Lost or compromised device | Tailscale identity, device removal, SSH authentication |
| Container to host | Excessive container privilege | Minimal mounts, avoid privileged mode, controlled capabilities |
| DNS clients to AdGuard | Service outage affects browsing | Stable addressing, restart policy, documented recovery |
| Server to backup disk | Ransomware or operator error | Separate physical disk, offline storage, integrity checks |
| Lab VM to trusted LAN | Malware escape or lateral movement | Isolated virtual network and snapshots |

## Data classifications

- **Public:** Sanitized diagrams and documentation in this repository.
- **Internal:** Hostnames, private addressing, inventory, and operational logs.
- **Secret:** Passwords, SSH private keys, OAuth credentials, access tokens,
  API keys, recovery codes, and service claim codes.

Only public material belongs in GitHub.

## Recovery strategy

1. Record service definitions and storage mappings.
2. Stop stateful containers during configuration snapshots.
3. Back up named volumes, bind mounts, media, and selected host configuration.
4. Restart services even when the backup workflow exits unexpectedly.
5. Generate SHA-256 hashes and verify them from the destination disk.
6. Perform a test restoration before depending on the backup for migration.

