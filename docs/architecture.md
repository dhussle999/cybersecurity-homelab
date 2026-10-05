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
| Server to SSD backup | Wrong disk, absent destination, data loss, host compromise | Explicit disk identification and erase confirmation, UUID/partition checks before writes, encrypted snapshots, integrity and restore verification; independent/offsite coverage still needed |
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

Encrypted Restic snapshots now go directly to SSD1 at /mnt/viki-backup,
mounted persistently by UUID. The existing service and timer run daily at
03:00 America/Chicago. The job checks the expected UUID, filesystem, and
physical partition before any backup writes and refuses to start below
20 GiB free. A missing-mount test verified refusal.

Scope includes application configuration and persistent state, Compose files,
Home Assistant, host configuration and personal files, and supported VM disks,
definitions, NVRAM, and TPM state. Application databases are copied while
their containers are stopped; Immich additionally has a logical PostgreSQL
dump. VM disks are copied only while shut down, with initially running guests
restarted after their stage.

The first repository used approximately 32 GiB. Full data integrity checking
and seven representative restores passed. Retention keeps 7 daily, 4 weekly,
and 2 monthly snapshots per host/tag group, with previews before deletion and
pruning. Complete application recovery and a restored VM boot remain untested,
as does the first unattended SSD run following migration.

The failing legacy drive is entirely excluded. SSD1 passed its extended test
but has historical errors and SMART log warnings. An SSD in the same host
shares exposure to compromise, theft, and power incidents; encryption does
not provide immutable or offsite protection.

The previous repository and phone copy are preserved for older files.
Server-side phone-transfer access was retired after SSD verification;
phone-side cron and boot-script removal commands were provided but their
execution is unconfirmed. The password must be preserved separately.

A larger NVMe backup destination and an encrypted iCloud copy remain planned.
This is a local file/application backup, not a bare-metal image or a complete
3-2-1 backup.

See [the detailed recovery project](viki-backup-recovery.md).
