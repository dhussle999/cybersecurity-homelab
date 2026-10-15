# VIKI — My Cybersecurity Homelab

I'm building VIKI to get hands-on experience with Linux, networking, and
cybersecurity. This repo is where I keep track of what I've installed, what
I've learned, and what I'm still working on.

## Tools and services I'm learning with

| Tool or service | What I use it for |
| --- | --- |
| Ubuntu | Linux administration |
| Docker and Portainer | Running and managing applications |
| Plex | Personal media |
| AdGuard Home | DNS filtering |
| Tailscale | Remote access |
| KVM / libvirt and Cockpit | Virtual machines and host management |
| Prometheus and node-exporter | Collecting and exploring metrics |
| Grafana | Monitoring dashboards |
| Homarr | Organizing service links and integrations |
| Wazuh | Security monitoring |
| OPNsense | Firewall and routing practice; configuration and testing in progress |
| Wireshark | Capturing and inspecting network traffic |

Installing a tool is my starting point. I'm using these projects to understand
how the tools work and how to troubleshoot them.

## What I've been working on

### Linux and self-hosting

I've installed and managed applications, worked with persistent data, and
practiced routine administration. Running services I actually use gives me
problems to work through and a reason to keep learning.

### Virtual machines

I created a Windows VM and started learning VM management. I still need to
finish documenting and testing its networking.

### Monitoring

I got my host monitoring dashboard working and fixed blank panels along the
way. I've also set up Wazuh and Homarr. I still want to build out alerting and
document a complete investigation exercise.

### Networking — in progress

I installed OPNsense and a managed switch to practice network segmentation
and firewall configuration. This project is still in progress.

I've been learning about VLANs, switching, access ports, trunks, and PVIDs.
I'm still working on routing and checking that the configuration behaves
the way I intended.

See [my VLAN and switching notes](docs/vlan-switching-progress.md).

### Packet inspection

I installed Wireshark and captured traffic while opening a web page. I stopped
the capture and looked through individual packets to start understanding what
happens when I browse. I haven't documented specific protocol findings yet.

### Troubleshooting

I've worked through performance and configuration problems instead of assuming
the first setup would work. One of my projects involved investigating a CPU
performance issue and checking the results after a firmware update.

### Automation and recovery

I've practiced scripting repetitive tasks and checking backup integrity and
sample restores. I'm still working toward complete recovery testing.

## What I've learned so far

- Installing something and understanding it are different milestones.
- An unmanaged switch forwards frames using learned MAC addresses; it isn't
  just a repeater.
- VLANs can separate wired devices as well as wireless clients.
- Separate physical ports don't automatically mean isolated networks.
- I need to test a configuration instead of assuming it works.
- A successful backup job doesn't prove a complete recovery will work.
- Troubleshooting is easier when I record what changed and compare results.

## What I'm working toward

- Finish my network segmentation project and document the test results.
- Get more comfortable reading packet captures.
- Build and test useful alerts.
- Document a security investigation from the initial alert through the outcome.
- Test full application and VM recovery.
- Keep improving my notes as I learn.

## About these notes

I keep this overview general and leave out specific network assignments,
addresses, hardware layouts, backup schedules, and detailed security settings.
Work in progress stays marked as work in progress.

I only test systems and accounts I own or have explicit permission to use.
