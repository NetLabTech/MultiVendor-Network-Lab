
MultiVendor Network Lab — Enterprise + ISP Simulation
A full multi‑vendor enterprise and ISP simulation lab built in EVE‑NG using Juniper EVO, Juniper vEX, Cisco vIOS, Cisco switches, Fortinet firewall, and VPC hosts.
This project demonstrates realistic WAN/LAN design, BGP peering, NAT, VLAN segmentation, firewalling, and multi‑vendor routing — ideal for CCNP‑level learning and MSP workflows.

📡 Topology Overview
This lab simulates:

A fake ISP router (Cisco vIOS) with loopback 8.8.8.8

A Juniper EVO WAN edge router

A Fortinet firewall providing security policy enforcement

A Juniper vEX distribution router

Two Cisco access switches

Multiple VPC clients

Full WAN + LAN routing, NAT, VLANs, and firewall policies

🧩 Devices Used
Cisco vIOS (ISP simulation)

Juniper EVO (WAN edge)

Fortinet Firewall

Juniper vEX (distribution layer)

Cisco IOSvL2 switches

VPC hosts

🌐 WAN / ISP Simulation
The ISP router provides:

Loopback: 8.8.8.8/32

WAN link: 203.0.113.2/30

BGP ASN: 65000

EVO peers with the ISP using:

WAN link: 203.0.113.1/30

BGP ASN: 65001

This creates a realistic enterprise‑to‑ISP edge.

🔐 Firewall Integration
The Fortinet firewall sits between EVO and vEX, enforcing:

Trust / Untrust zones

NAT policies

Security rules

Logging and inspection

Traffic flows:

EVO → Fortinet → vEX → Cisco switches → VPCs

🏠 LAN Design
The LAN includes:

VLAN segmentation

Inter‑VLAN routing

Access switching

VPC clients for testing

DHCP (optional)

DNS (optional)

📘 Key Technologies Demonstrated
BGP peering

NAT

VLAN segmentation

Firewall policies

Enterprise routing

ISP simulation

Multi‑vendor interoperability

EVE‑NG topology design
