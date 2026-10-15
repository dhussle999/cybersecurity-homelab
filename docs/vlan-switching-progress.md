# VLANs, managed switching, and OPNsense — progress record

Updated: October 15, 2026  
Status: In progress

## Objective

Build a segmented network with three intended VLANs on the managed switch:

| Planned VLAN | Purpose | Status |
| --- | --- | --- |
| VIKI | Homelab server and services | In progress |
| AP | Dedicated access point / wireless lab network | Planned within the current project |
| Malware lab | Separate environment for authorized malware detection, monitoring, and remediation practice | Isolation not yet validated |

These are functional names; final numeric VLAN IDs and port assignments still
need confirmation. The AP management network and wireless client VLAN handling
will be documented when the AP configuration is established.

Use managed switching to assign devices to VLANs and OPNsense to control routed
traffic between networks. The goal is to reduce opportunities for lateral
movement while keeping authorized administration available.

## Current progress

- Installed a NETGEAR GS308E managed switch and began working with switch
  management, port assignments, and VLAN concepts.
- Installed OPNsense in the Ubuntu/KVM lab environment. Configuring internet
  traffic to pass through OPNsense is still in progress.
- Began planning VLAN separation for VIKI and a dedicated learning PC.
  Port 1 was identified for VIKI; port 8 and VLAN 10 were discussed for the
  learning PC. These are planning references, not a verified running port map.
- Installed Wireshark and practiced capturing traffic while opening a web page,
  stopping the capture, and inspecting individual packets. Specific protocol
  findings and capture evidence have not yet been documented.

Progress here reflects hands-on work and learning reported by the lab owner.
This record does not establish that VLAN isolation, firewall enforcement, or
the complete OPNsense internet path has passed validation.

## What I learned about switching and VLANs

| Concept | My understanding and practical use |
| --- | --- |
| Ethernet switching | A switch learns source MAC addresses and forwards frames according to its MAC address table. An unmanaged switch still performs switching; it is not simply a repeater. |
| VLANs | VLANs create separate Layer 2 broadcast domains on shared switching hardware. They can separate wired devices as well as wireless clients. |
| Managed switch ports | VLAN membership and handling of untagged traffic determine which network a connected device joins. Separate physical ports alone do not establish separate VLANs. |
| Access-style port | A device-facing port commonly carries one VLAN untagged. The PVID classifies incoming untagged frames; egress membership and tagging also need to match the design. |
| Trunk-style link | A link can carry multiple VLANs using 802.1Q tags. The switch and the connected firewall, host, or AP must agree on VLAN handling. |
| OPNsense firewall | Routing provides a path between subnets; firewall rules decide which routed connections are allowed. VLAN separation needs an explicit traffic policy and testing. |
| Same-VLAN traffic | Devices in the same VLAN can communicate through the switch without crossing the OPNsense inter-VLAN firewall. |
| Virtualized firewall | The host bridges, virtual NICs, and physical uplinks must preserve the intended path so traffic does not bypass OPNsense. |

The useful model for me is that the switch assigns devices to separate network
areas, while OPNsense controls permitted movement between those areas when it
routes their traffic.

## Intended traffic policy — not yet validated

- Keep the learning network separate from VIKI services and the trusted home LAN.
- Permit administration only from explicitly authorized devices or networks.
- Allow only the DNS, update, and other connectivity required for each exercise.
- Keep risky testing disconnected until the intended containment is verified.

The final VLAN IDs, subnet plan, switch membership, uplink tagging, and firewall
rules still need to be recorded from the actual configuration.

## Validation work remaining

| Check | Evidence to record | Status |
| --- | --- | --- |
| Switch VLAN membership and PVIDs | Sanitized port/VLAN table | Pending |
| Client addressing | Expected subnet, gateway, and DNS for each test device | Pending |
| OPNsense internet path | Client routing details and matching firewall/interface observations | Pending |
| Approved management access | Successful connection from an authorized source | Pending |
| Denied cross-VLAN access | Failed connection attempts paired with relevant firewall logs | Pending |
| No alternate path to trusted networks | Review host bridges, routing, and additional device interfaces | Pending |
| Persistence | Repeat key checks after a controlled restart | Pending |

Record observed results, including failures and fixes. Packet captures and
screenshots should be reviewed and sanitized before publication.

## Current outcome

The managed switch, OPNsense installation, and initial Wireshark practice are
part of the lab's current progress. The next milestone is a documented,
tested traffic path and VLAN policy. Network isolation remains unverified.
