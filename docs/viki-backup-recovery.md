# VIKI encrypted SSD backups and recovery

Status verified: 2026-10-05. Public documentation omits credentials, addresses,
account names, disk serials and UUIDs, VM identifiers, and personal filenames.

## Current status

VIKI now writes encrypted Restic backups directly to SSD1 instead of depending
on the unfinished Android phone-transfer workflow.

| Item | Verified configuration or result |
| --- | --- |
| Destination | Existing main partition on a 240 GB SATA SSD, formatted as ext4 |
| Mount | /mnt/viki-backup, persisted in fstab using the filesystem UUID |
| Repository | /mnt/viki-backup/repository |
| Schedule | Existing viki-backup.service and viki-backup.timer; daily at 03:00 America/Chicago |
| Retention | 7 daily, 4 weekly, 2 monthly snapshots per host/tag group |
| Initial repository | Approximately 32 GiB, with approximately 184 GiB available |
| Integrity | Full encrypted repository data check passed |
| Restore checks | Seven representative files restored to a private temporary directory and matched recorded SHA-256 hashes |
| Missing destination | A private mount-namespace test proved that an absent disk causes refusal before backup writes |
| Running services | Applications resumed; the Windows VM retained its initially shut-off state |
| Phone workflow | Server-side transfer access retired after SSD verification; phone-side schedule removal commands supplied, execution unconfirmed |

The first SSD backup was run and verified manually. The adapted timer is
enabled and active; its next scheduled run still needs observation. This is
local file/application backup, not a bootable bare-metal image or a complete
3-2-1 backup.

## Disk identification and migration controls

Before erasing anything, inventory correlated the SSD mount with the physical
disk model, serial, capacity, partition, and filesystem UUID. Existing Windows
folders, space usage, fstab entries, Docker mounts, and backup configuration
were inspected. The owner explicitly confirmed erasing that specific main
partition. The other disk and Ubuntu system disk were never formatted, and
the remaining partitions on SSD1 were retained.

SSD1 reported an overall SMART status of PASSED, zero reallocated sectors,
116 historical reported uncorrectable errors, and error-log checksum warnings.
An extended test completed without error, with the error count unchanged.
That result supported proceeding but does not erase the reliability concern.
Another independent healthy backup destination and continued monitoring are
still needed.

A conservative source allocation estimate was checked before formatting.
The installer safely unmounted the confirmed partition, formatted only that
partition as ext4, and mounted it by its new UUID. It adapted the existing
service and timer rather than creating a second schedule.

The job checks the exact mount point, expected UUID, ext4 filesystem, and
physical partition before creating backup directories, a cache, or a lock.
It refuses to begin with less than 20 GiB free. These controls prevent an
absent SSD from redirecting backups into a directory on the system disk.

## Verified backup scope

| Stage | Included state | Consistency method |
| --- | --- | --- |
| DNS | AdGuard Home configuration and persistent working data | Stop included application, back up, restore prior running state |
| Applications | Home Assistant, Vaultwarden, Immich, Homarr, Plex configuration, Grafana, Prometheus, Portainer, and VIKI alert configuration/state | Stop containers with included persistent mounts, capture state, restart those initially running |
| Database export | Immich PostgreSQL logical SQL dump in addition to the stopped physical database copy | Quiesce the photo application and require a successful nonempty pg_dumpall export |
| VM | Windows VM disk and supported backing chains, inactive definition, NVRAM, and TPM state | Copy only while shut down; gracefully stop and restart initially running supported guests |
| Host and personal files | /etc, /boot, /root, /home, /opt, /srv, /usr/local, Compose files, scripts, and recovery inventories | File capture with explicit exclusions |

Unsupported block/network VM storage or detected user-session VMs cause a
failure rather than an unsupported claim of coverage. A running guest must
complete graceful shutdown; the workflow does not force a shutdown to obtain
a disk copy.

The actual VM was already shut off during the initial SSD backup. Its disk
data passed the full repository check and its XML was restored, but a complete
VM restoration and boot have not been tested.

Personal files can change during the scan; they do not form one atomic
filesystem-wide snapshot.

## Integrity, restores, and retention

The initial backup produced five stage snapshots. Full repository verification
read all encrypted pack data. Representative restores covered the PostgreSQL
SQL dump, Vaultwarden database, Home Assistant configuration, VM definition,
fstab, and two Compose files. Each restored file passed Restic verification
and matched a SHA-256 hash captured before its backup.

Application database import, complete application recovery, and a VM boot
test remain separate future exercises. Successful file verification does not
claim those exercises are complete.

The first repository used approximately 15% of the destination filesystem,
supporting 7 daily, 4 weekly, and 2 monthly restore points per host/tag group.
Unchanged data is shared across snapshots. Retention is a policy, not a promise
that future data growth will fit indefinitely.

Before removing snapshots, the job saves a forget preview. It then saves a
prune preview before deleting unreferenced repository data and checks integrity
afterward. Initial pruning removed nothing. Daily runs check repository
structure; Sunday runs also read all repository data. Low-space refusal is
reported as a failure rather than a successful backup.

See the official [Restic retention documentation](https://restic.readthedocs.io/en/stable/060_forget.html)
and [restore documentation](https://restic.readthedocs.io/en/stable/050_restore.html).

## Exclusions and remaining gaps

- All data on /mnt/ssd2 and links pointing there, including the personal-photo
  link and Plex music source. This disk has known read errors.
- Old Windows data erased from SSD1. Only anything already captured in the
  preserved previous repository remains protected there.
- The active repository and its cache, /var/lib/viki-backup, old export and
  backup folders, previous recovery folders, and temporary restore folders.
- /proc, /sys, /dev, /run, container sockets, host root mounts exposed to
  monitoring containers, temporary files, and application/system caches.
- Ollama model downloads, the Immich machine-learning cache, and llama.cpp
  build output.
- Movie and TV media folders.
- Container images and disposable writable layers; persistent mounted state
  and Compose configuration are covered.
- VM installation CD-ROM ISOs and reinstallable firmware packages.
- An offsite copy, offline/immutable protection, bare-metal recovery image,
  full application recovery rehearsal, and complete VM boot verification.

The local coverage manifest lists exact included paths, exclusion patterns,
and discovered skipped sources. It is not uploaded publicly.

## Recovery and password preservation

The existing repository password was retained and secured as a root-owned
file with mode 0600. Keep a separately accessible password-manager entry and
a secure offline copy. A password stored only inside this encrypted repository
cannot unlock it during recovery. Do not rely exclusively on a password
manager running on this same server.

List snapshots:

```bash
sudo restic -r /mnt/viki-backup/repository --password-file /etc/viki-backup/password snapshots
```

Restore a selected snapshot into a separate private directory:

```bash
sudo mkdir -m 700 /root/viki-restore
sudo restic -r /mnt/viki-backup/repository --password-file /etc/viki-backup/password restore SNAPSHOT_ID --target /root/viki-restore --verify
```

Select all needed stage tags: viki-dns, viki-apps, viki-databases, viki-vms,
and viki-host-personal. On another Linux host, mount the existing SSD repository
and supply the independently preserved password. Do not initialize a new
repository over the existing one.

Stop affected applications before restoring their data to original paths.
Restore permissions, Compose files, and configuration secrets. PostgreSQL
recovery can use the stopped physical cluster with a compatible version or
the logical SQL dump imported into an empty compatible database.

Restore VM disks and backing files, NVRAM, TPM state, and the saved XML
definition while the VM is shut down. Define the VM with libvirt after paths
are restored. Never overwrite a running guest disk.

## Previous phone backup

The earlier encrypted server repository remains at
/var/lib/viki-backup/export/repository. The phone previously created a verified
generation, but its later synchronization encountered a hard-link permission
failure and final unattended operation was never established.

After SSD verification, the server-side phone-control sudo rule and tagged
phone SSH key were retired. Exact Termux commands were provided to remove the
phone cron entry and disable its boot script and launcher. Their execution
on the phone is not yet confirmed.

Preserve the old phone repository and its password until it has been checked
for unique older files and another independent backup is established. It may
contain old Windows personal files that are no longer on the reformatted SSD.
Keeping it does not establish a current offsite replica.

## Earlier troubleshooting and future work

The original Windows shutdown timeout was resolved by configuring the QEMU
guest agent and confirming guest communication. Read failures on the other
legacy drive were investigated through kernel and SMART evidence; that drive
remains excluded, and unreadable files have not been recovered by this work.

Next steps are to observe the first unattended SSD run, confirm phone-side
automation removal, preserve the password independently, monitor SSD health,
rehearse an application and VM recovery, and add another healthy independent
backup plus verified offsite coverage. A larger NVMe destination and encrypted
iCloud copy remain plans, not deployed protection.

## Skills demonstrated

Linux disk identification, controlled filesystem migration, UUID-based mounts,
systemd scheduling, Docker storage inventory, consistent database exports,
VM lifecycle management, encrypted versioned backups, integrity checks,
representative restore testing, and explicit coverage reporting.
