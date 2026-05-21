# Virtual Lan Networking Lab

## Overview

This lab simulates a small office network environment built entirely on VirtualBox. It covers core networking concepts that are directly applicable to IT support and helpdesk roles — DNS, DHCP, remote access, and network traffic analysis.

---

## Lab Environment

| Component | Details |
|-----------|---------|
| Hypervisor | Oracle VirtualBox |
| Server OS | Ubuntu Server 26.04 LTS |
| Client OS | Windows 11 25H2 Home |
| Network Type | Internal Network (labnet) |
| Host OS | Windows 11 (host machine) |

### Network Topology

<!-- INSERT: network-diagram.png (draw.io export) -->

| Device | Hostname | IP Address | Role |
|--------|----------|------------|------|
| Ubuntu Server | UbuntuServer-26-IT-P2 | 192.168.10.1 | DNS, DHCP, SSH Server |
| Windows 11 VM | Windows11-IT-P2 | 192.168.10.50 | Client machine |

---

## What Was Built

### Phase 1 — Virtual LAN Setup
Configured two VMs on an isolated Internal Network in VirtualBox. Assigned a static IP to the Ubuntu Server via Netplan and verified connectivity between both machines.

<!-- INSERT: screenshots/network-setup/ping-reply.png -->
> *Ping from Windows 11 VM to Ubuntu Server (192.168.10.1) showing successful replies*

---

### Phase 2 — DNS and DHCP

#### DNS — BIND9
Installed and configured BIND9 on Ubuntu Server as the internal DNS server for the `lab.local` domain. Created a forward zone with A records resolving `server.lab.local` to `192.168.10.1`.

<!-- INSERT: screenshots/dns-dhcp/nslookup-server-lab-local.png -->
> *nslookup server.lab.local resolving to 192.168.10.1*

#### DHCP — isc-dhcp-server
Installed and configured isc-dhcp-server on Ubuntu to automatically assign IPs to devices joining the network. Configured the DHCP pool to assign addresses in the `192.168.10.50–192.168.10.100` range.

<!-- INSERT: screenshots/dns-dhcp/windows-ipconfig-dhcp.png -->
> *ipconfig on Windows 11 VM showing DHCP-assigned IP 192.168.10.50*

---

### Phase 3 — Remote Access

#### SSH
Installed OpenSSH server on Ubuntu. Connected from the host machine via PuTTY and PowerShell using NAT port forwarding (host port 2222 → VM port 22).

<!-- INSERT: screenshots/remote-access/putty-ssh-session.png -->
> *PuTTY SSH session logged into Ubuntu Server*

<!-- INSERT: screenshots/remote-access/powershell-ssh-session.png -->
> *PowerShell SSH session into Ubuntu Server*

#### VNC Remote Desktop
Installed TightVNC Server on Windows 11 VM. Connected from host machine using TightVNC Viewer via NAT port forwarding (host port 5900 → VM port 5900).

> **Note:** Windows 11 Home edition does not support hosting RDP connections. TightVNC was used as an alternative — it provides equivalent remote desktop functionality using the VNC protocol, which is widely used in Linux environments and cross-platform support scenarios.

<!-- INSERT: screenshots/remote-access/vnc-windows11-session.png -->
> *TightVNC Viewer connected to Windows 11 VM desktop*

---

### Phase 4 — Network Traffic Analysis with Wireshark

Wireshark was installed on the Windows 11 VM to capture traffic directly on the Internal Network. This approach was chosen because the host machine accesses VMs via NAT port forwarding and cannot observe inter-VM traffic directly.

#### ICMP — Ping Traffic
Captured ping packets from Windows 11 VM to Ubuntu Server using the `icmp` filter.

<!-- INSERT: screenshots/wireshark/wireshark-icmp.png -->
> *Wireshark icmp filter showing ping request and reply packets between 192.168.10.50 and 192.168.10.1*

#### DNS — Name Resolution Traffic
Captured DNS query and response when pinging `server.lab.local` from Windows 11 VM using the `dns` filter.

<!-- INSERT: screenshots/wireshark/wireshark-dns.png -->
> *Wireshark dns filter showing DNS query for server.lab.local and response returning 192.168.10.1*

#### SSH — TCP Traffic
Captured SSH handshake and encrypted session traffic using the `tcp.port == 22` filter.

<!-- INSERT: screenshots/wireshark/wireshark-ssh.png -->
> *Wireshark tcp.port==22 filter showing SSH handshake between client and Ubuntu Server*

---

## IT Support Scenarios This Prepares For

| Scenario | Skill Demonstrated |
|----------|-------------------|
| User reports "I can't connect to the file server" | Ping test, DNS resolution check |
| New device joins the network and needs an IP | DHCP configuration and troubleshooting |
| Remote support for a user's machine | SSH for servers, VNC/RDP for desktops |
| Diagnosing intermittent network issues | Wireshark packet capture and analysis |
| Internal hostname not resolving | DNS zone file configuration |

---

## Key Concepts

**DNS vs DHCP**
- DNS answers "what IP is this hostname?" — configured via BIND9 with zone files and A records
- DHCP answers "what IP should this device use?" — configured via isc-dhcp-server with an IP pool

**SSH vs VNC/RDP**
- SSH is used for remote command-line access to servers — lightweight, encrypted, standard in Linux environments
- VNC/RDP is used for remote desktop access to GUI machines — used in helpdesk support scenarios

**Packet Capture**
- Wireshark captures raw network traffic and allows filtering by protocol
- Useful for diagnosing connectivity issues, verifying services are running, and understanding network behaviour

---

## Repository Structure

```
it-support-lab-p2/
├── README.md
├── /docs
│   ├── network-diagram.md
│   ├── dns-dhcp-setup.md
│   ├── remote-access.md
│   └── wireshark-analysis.md
└── /screenshots
    ├── /network-setup
    ├── /dns-dhcp
    ├── /remote-access
    └── /wireshark
```

---

## Tools Used

| Tool | Purpose |
|------|---------|
| VirtualBox | Hypervisor for running VMs |
| BIND9 | DNS server |
| isc-dhcp-server | DHCP server |
| OpenSSH | SSH server |
| PuTTY | SSH client |
| TightVNC | VNC remote desktop |
| Wireshark | Network packet capture and analysis |