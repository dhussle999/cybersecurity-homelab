# Interview talking points

## Tell me about your homelab

I built a bare-metal Ubuntu server that runs continuous services through Docker.
I use Tailscale for private remote administration, AdGuard Home for DNS filtering,
Portainer for container visibility, and Plex for personal media management. I
designed persistent storage and backup procedures so applications can be rebuilt
without losing their state. I also installed KVM/libvirt and Cockpit and created
a Windows VM on the Ubuntu host.

## Describe a hardware troubleshooting problem

My Intel Core Ultra 5 250K Plus ran at about 400 MHz during a sustained load
instead of boosting. I measured the behavior with turbostat, checked that CPU
temperatures were low, and updated the Gigabyte B860 DS3H WIFI6E rev. 1.0 BIOS
through Q-Flash. A repeat load test showed approximately 4.6–5.1 GHz at full
CPU utilization. I documented the before/after measurements so the resolution
is reproducible and does not rely on how fast the UI felt.

## What security problem did you solve?

I needed remote access without exposing administrative interfaces directly to
the public internet. I deployed an identity-based encrypted Tailscale network,
limited management access to approved devices, and documented a process for
removing lost or untrusted devices.

## Describe an automation project

I created a Python Gmail API utility using OAuth 2.0. It searches messages using
age and unread-status rules, handles paginated results, previews the impact, and
uses explicit confirmation before applying changes. I treated destructive
automation as a safety problem, not only a coding problem.

## How do you approach backups?

I distinguish synchronization from recovery. My migration workflow records an
inventory, temporarily stops stateful containers, captures Docker volumes and
bind mounts, restarts services, copies media to a separate physical disk, and
generates SHA-256 checksums. My next step is testing restoration onto a clean VM.

## What would you improve next?

I would add power-loss protection, automated patching, centralized monitoring,
and a SIEM. I plan to verify isolation for the Windows VM before risky testing and keep
future malware-analysis VMs separate from the trusted household network and document detection and response exercises.

