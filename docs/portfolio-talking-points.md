# Interview talking points

## Tell me about your homelab

I built a bare-metal Ubuntu server that runs continuous services through Docker.
I use Tailscale for private remote administration, AdGuard Home for DNS filtering,
Portainer for container visibility, and Plex for personal media management. I
designed persistent storage and backup procedures so applications can be rebuilt
without losing their state.

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
and a SIEM. I also plan to isolate Windows and malware-analysis VMs from the
trusted household network and document detection and response exercises.

