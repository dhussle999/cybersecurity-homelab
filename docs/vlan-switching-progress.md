# My VLAN and switching project

Updated: October 15, 2026  
Status: In progress

## What I'm working on

I installed OPNsense and a managed switch to practice network segmentation
and firewall configuration. This project is still in progress.

My goal is to understand how to separate network traffic, control communication
between networks, and test whether my configuration works the way I intended.

## What I've done so far

- Installed a managed switch and started learning its management interface
  and VLAN settings.
- Installed OPNsense in my lab. I'm still working on routing internet traffic
  through it.
- Started planning my network segmentation.
- Installed Wireshark and practiced capturing traffic while opening a web page.
  I stopped the capture and inspected individual packets to start understanding
  what happens on the network when I browse. I haven't added specific protocol
  findings or capture screenshots yet.

## What I've learned

I originally thought an unmanaged switch was basically a repeater. I learned
that it learns MAC addresses and forwards frames using its MAC address table.
A managed switch gives me additional control over how traffic is organized.

I also learned that VLANs apply to wired devices, not just Wi-Fi. Plugging
devices into different physical ports doesn't automatically isolate them.

| Concept | How I understand it now |
| --- | --- |
| VLAN | A separate Layer 2 broadcast domain on shared switching hardware |
| Access-style port | A device-facing port commonly carrying one VLAN untagged |
| PVID | The VLAN used to classify incoming untagged frames |
| Trunk-style link | A link carrying multiple VLANs using 802.1Q tags |
| Routing and firewall rules | Routing provides a path between networks; firewall rules decide which routed connections are allowed |
| Same-VLAN traffic | Devices can communicate through the switch without crossing an inter-VLAN firewall |
| Virtualized networking | Bridges and virtual interfaces need to match the intended traffic path |

The way I picture it is that the switch puts devices into separate areas,
and the firewall controls movement between those areas when it routes traffic.
I still need to check the actual configuration instead of assuming the
separation works just because I created VLANs.

## What I'm doing next

- Finish the segmentation and routing configuration.
- Check that devices receive the expected network settings.
- Test connections that should be allowed and connections that should be blocked.
- Compare my test results with firewall logs.
- Repeat the main checks after a controlled restart.
- Add sanitized screenshots and write down any problems I run into and how I fix them.

## Where I'm at now

I've installed the tools and started learning how they work together.
Routing and isolation testing are still in progress. I'll update these notes
with actual results as I complete the checks.
