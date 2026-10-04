# VIKI encrypted backups and recovery troubleshooting

Status recorded: 2026-10-04. This record separates observed outcomes from
intended automation. Addresses, account names, device serials, VM identifiers,
passwords, and personal filenames are omitted.

## Objective and current status

Use an existing 512 GB Android phone as a separate encrypted backup destination
for the Ubuntu homelab, with repeatable backup stages and recovery checks.

| Milestone | Evidence and status |
| --- | --- |
| Initial application archive transfer | Two dated archive sets totaling approximately 4.29 GB transferred to the phone; checksum results were not recorded |
| Encrypted server backup | Installer progressed past its successful first server run |
| Daily server schedule | systemd timer enabled for 03:00 America/Chicago |
| Windows guest agent | guest-ping returned an empty success response |
| Failing HDD exclusion | Updated backup scope excludes the legacy drive and recovery-image directory |
| Phone repository | A verified generation was created; the second synchronization failed while creating hard links |
| Phone workaround | Normal-copy replacement provided; execution and successful retry not confirmed |
| Phone cron and reboot startup | Final installer success and Termux:Boot startup test not confirmed |
| Restore testing | File, application, and full-system restoration still pending |
| NVMe and offsite copies | Planned, not implemented |

## Backup design

Restic encrypts the repository and stores versioned snapshots. The scope is
selected recoverable state rather than a bootable whole-disk image.

| Stage | Included state | Consistency approach |
| --- | --- | --- |
| DNS | AdGuard Home configuration and persistent working data | Briefly stop AdGuard, capture state, restart |
| Applications | Homarr, Vaultwarden, Plex configuration, Grafana, Prometheus, Immich data/database, Portainer, and service definitions | Stop the containers that were running, capture persistent mounts, restore their running state |
| Virtual machines | Supported file-backed VM disks, backing chains, VM definitions, and relevant firmware/TPM state | Graceful guest-agent shutdown, capture storage, restart initially running guests |
| Host and personal data | Host configuration, boot files, administrative scripts, package/service inventory, and selected home/personal folders on healthy storage | File-level capture with explicit exclusions |

Disposable caches, virtual filesystems, container sockets, redundant backup
outputs, the excluded failing drive, and recovery images are omitted. Password
manager data and configuration secrets stay inside the encrypted backup.

The phone authenticates with an SSH key and pulls the encrypted repository with
rsync. The intended process stages a candidate, runs `restic check --read-data`,
confirms expected snapshot IDs, then promotes that candidate as the current
verified copy. A failed candidate should leave the previous verified generation
available.

Server retention is designed to wait for acknowledgment from the verified
phone copy, keeping seven daily restore points per host/tag group and at least
one latest snapshot. This is not a claim that seven scheduled runs or restores
have already been tested.

## Troubleshooting record

### Windows VM shutdown timeout

The first server run backed up DNS and applications, then failed because the
Windows VM did not shut down within 180 seconds. No forced shutdown was used.
The initial guest-agent probe reported that the agent was not configured.

A persistent virtio guest-agent channel was attached and Windows guest tools
were installed. A subsequent guest-ping succeeded. The backup shutdown command
was changed to explicitly use guest-agent mode. A later server run completed
and enabled the timer.

### Personal-file I/O errors and failing legacy HDD

Restic reported unreadable personal files on the legacy Windows-data drive.
Kernel logs showed repeated medium errors, unrecovered reads, and UNC/AMNF
errors at specific sectors. SMART reported ten pending sectors and 1,023 ATA
errors even though its overall health label read PASSED.

Inventory identified a 250 GB Fujitsu mechanical HDD, despite the mount being
named as though it were an SSD. These observations supported treating the disk
as failing. A recovery-image approach was discussed, but completion was not
confirmed. The chosen operational change was to exclude the drive from routine
backups and replace it later.

A subsequent run still read that drive, so the updated exclusion needed to be
applied through the correct installer/server code. The later successful server
run was observed after this troubleshooting. Exclusion prevents routine backup
reads; it does not recover unreadable files or protect data remaining only on
that disk.

### Phone hard-link permission failure

The phone reached a verified repository generation, then failed during its
second synchronization. The log showed `cp -al` returning Permission denied
while cloning repository files into the incoming candidate.

A workaround was provided to replace hard-link cloning with ordinary
`cp -a` copying in the phone engine and downloaded installer. This requires
space for another repository copy while verification runs. Free-space output
and a successful rerun are still needed before marking phone automation
complete. The verified generation should be preserved during troubleshooting.

### Installer execution location

The installer runs in Termux on the phone and connects to Ubuntu over SSH.
It must be run from the phone shell using the updated downloaded file. Running
a stale download can reinstall earlier behavior. A closed SSH connection after
the server stage is normal when the installer proceeds to the phone transfer.

## Remaining validation

- Confirm sufficient phone space and successful normal-copy synchronization.
- Confirm the final installer success message, verified marker, and phone cron.
- Install Termux:Boot from the matching Termux distribution, open it once,
  configure background operation, and test startup after reboot and unlock.
- Restore a sample file to a separate directory and compare its contents.
- Test recovery of a stateful application and a VM without overwriting production.
- Keep a recoverable copy of the encryption password separate from the repository.
- Observe a scheduled daily run and phone transfer before relying on unattended operation.

## Planned parallel and offsite copies

A future 1 TB NVMe will provide another local backup destination for faster
recovery and extra capacity. If installed in the same host, it shares exposure
to host compromise, theft, and power incidents; it does not provide offsite
protection.

An encrypted VIKI repository copy in iCloud is deferred. Existing iCloud
photos/documents do not automatically cover the homelab applications and VMs.
The offsite step requires copying the complete encrypted repository, confirming
upload completion, preserving the password separately, and testing restoration.

The 3-2-1 goal is three copies of the same important data, two media types, and
one offsite copy. Current logs do not establish that goal as complete. Phone
and NVMe copies provide device diversity, while strict media diversity and an
offline or immutable recovery copy still need planning.

## Skills demonstrated

Linux service scheduling, Docker storage inventory, encrypted versioned
backups, SSH automation, VM lifecycle management, kernel/SMART diagnosis,
failure-aware retention, and honest recovery-status reporting.
