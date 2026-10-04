# VIKI architecture and threat model

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
| Server to phone backup | Ransomware, operator error, interrupted transfer | Encrypted repository, separate device, verification before promotion; automation validation pending |
| Lab VM to trusted LAN | Malware escape or lateral movement | Configure and verify an isolated virtual network and snapshots before risky tests |

## Virtualization

Ubuntu remains the physical host. KVM/libvirt provides virtual machines and
Cockpit with cockpit-machines provides browser-based VM management. A Windows VM
has been created for learning. The QEMU guest agent is configured and responds to guest-ping for backup
lifecycle management. Network isolation remains unverified for risky workloads.

## Data classifications

- **Public:** Sanitized diagrams and documentation in this repository.
- **Internal:** Hostnames, private addressing, inventory, and operational logs.
- **Secret:** Passwords, SSH private keys, OAuth credentials, access tokens,
  API keys, recovery codes, and service claim codes.

Only public material belongs in GitHub.

## Recovery strategy

The server creates encrypted Restic snapshots and has a daily systemd timer
enabled for 03:00 America/Chicago. Backup scope includes consistent application
data, file-backed VM storage and recovery metadata, host configuration, and
selected personal data on healthy storage. Containers and initially running VMs
are restarted after their backup stages.

The phone workflow pulls the encrypted repository over SSH, performs a full
Restic data check, and promotes the candidate only after required snapshots
are present. Retention is designed to run after phone acknowledgment. The first
verified phone generation exists, but the second synchronization encountered a
hard-link permission error; final automation and reboot startup remain pending.

The failing legacy HDD is excluded. Its data is outside current routine backup
coverage. A 1 TB NVMe parallel local copy and an encrypted offsite iCloud copy
are planned. Neither offsite coverage nor a tested full restore is complete.

See [the detailed recovery project](viki-backup-recovery.md).
