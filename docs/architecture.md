# Architecture and threat model

## Design objective

The homelab provides continuously available household services while creating
a controlled environment for infrastructure and defensive-security learning.
Ubuntu runs directly on the hardware. Long-running applications use Docker,
while KVM/libvirt runs a Windows VM managed with Cockpit. Isolation for any
higher-risk learning workloads remains a future task; VM networking has not yet
been verified for those workloads.

## Trust boundaries

| Boundary | Risk | Mitigation |
| --- | --- | --- |
| Internet to home network | Unsolicited access | Avoid exposing management interfaces publicly |
| Remote device to server | Lost or compromised device | Tailscale identity, device removal, SSH authentication |
| Container to host | Excessive container privilege | Minimal mounts, avoid privileged mode, controlled capabilities |
| DNS clients to AdGuard | Service outage affects browsing | Stable addressing, restart policy, documented recovery |
| Server to backup disk | Ransomware or operator error | Separate physical disk, offline storage, integrity checks |
| Lab VM to trusted LAN | Malware escape or lateral movement | Configure and verify an isolated virtual network and snapshots before risky tests |

## Virtualization

Ubuntu remains the physical host. KVM/libvirt provides virtual machines and
Cockpit with cockpit-machines provides browser-based VM management. A Windows VM
has been created for learning. Its boot state, guest configuration, and network
isolation have not yet been documented or verified in this repository.

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

