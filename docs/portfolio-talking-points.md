# Interview talking points

## Tell me about your homelab

I built VIKI, a bare-metal Ubuntu server that runs continuous services through Docker.
I use Tailscale for private remote administration, AdGuard Home for DNS filtering,
Portainer for container visibility, and Plex for personal media management. I
designed persistent storage and backup procedures so applications can be rebuilt
without losing their state. I also installed KVM/libvirt and Cockpit and created
a Windows VM on the Ubuntu host. I deployed Prometheus, node-exporter, and
Grafana in Docker and brought an Ubuntu Host Overview dashboard online.

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

I implemented daily encrypted Restic backups to a dedicated SSD partition,
covering Docker application state, Compose files, Home Assistant, personal
files, and Windows VM disks and recovery metadata. I identified the disk
through its model, serial, partition, and mount rather than assuming a device
name. I checked dependencies, capacity, and SMART health, obtained explicit
erase confirmation, and mounted the reformatted partition by UUID.

The existing systemd timer runs at 3 AM America/Chicago. Backup jobs check the
expected mount and physical partition before writing; I verified that a missing
destination causes refusal. I use stopped application database copies plus
an Immich PostgreSQL logical export, and copy VM disks only while shut down.

The initial repository used about 32 GiB with about 184 GiB available. Full
repository data verification passed, and seven representative restores matched
recorded SHA-256 hashes. Retention keeps 7 daily, 4 weekly, and 2 monthly
snapshots per group, with previews before pruning.

I previously diagnosed a Windows shutdown timeout and configured the guest
agent. I also investigated personal-file I/O errors and excluded a failing
legacy drive. The unfinished phone-transfer design was replaced by the SSD
destination; old backups and passwords remain useful for unique older files.

I distinguish verified file restores from full recovery: an application
recovery rehearsal, restored VM boot, first unattended SSD run, and independent
offsite coverage remain work to do. SSD1 passed its extended test but retains
historical errors, which makes another healthy backup especially important.
See [the project record](viki-backup-recovery.md).

## Describe your monitoring project

I use node-exporter to expose Ubuntu host metrics, Prometheus to collect and
query them, and Grafana to visualize CPU, memory, disk, network, uptime, load,
and exporter health. I troubleshot initially blank panels and got the Ubuntu
Host Overview dashboard displaying host metrics. This gives me a baseline for
resource pressure and host health. Service probes, alert delivery, centralized
logs, and a documented detection exercise are the next milestones; I do not
present them as completed.

## What would you improve next?

I would extend the working host dashboard with DNS and application availability
probes, tested alert notifications, and centralized logs. Automated security
updates, graceful shutdown on power loss, VM snapshot rotation, and a SIEM
remain follow-up work. I plan to verify Windows VM isolation before risky
testing, keep future malware-analysis VMs separate from the trusted household
network, and document a detection and response exercise.

