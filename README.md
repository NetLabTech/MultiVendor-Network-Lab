Multi-Vendor Network Lab (EVE-NG)

Multi-vendor lab environment covering routing, switching, and security design across Juniper, Cisco, and Fortinet platforms, built on bare-metal EVE-NG.

Scope
Cross-vendor interop and troubleshooting (Juniper / Cisco / Fortinet)
Core → distribution → security → access design
Junos (JNCIS-ENT / JNCIP-ENT level) alongside Cisco IOS-XE
FortiGate HA pair with dedicated sync link
Topology


![Topology Diagram](...)


Design:

Core (Juniper vMX pair): VCP/VFP pair, full mesh to both CSRs
Distribution (Cisco CSR1000v pair): dual-homed to both vMX nodes, mesh + dynamic routing for failover
Security perimeter (FortiGate pair): 1:1 links only, dedicated HA sync link between the two
Access/distribution switching (Cisco IOL L3): one per firewall, 1:1, VLANs/SVIs to follow

Design:

Core (Juniper vMX pair): VCP/VFP pair, full mesh to both CSRs
Distribution (Cisco CSR1000v pair): dual-homed to both vMX nodes, mesh + dynamic routing for failover
Security perimeter (FortiGate pair): 1:1 links only, dedicated HA sync link between the two
Access/distribution switching (Cisco IOL L3): one per firewall, 1:1, VLANs/SVIs to follow
Devices & Images
Role	Device	Image	Notes
Core router (control plane)	vMX-VCP / vMX-VCP2	vmxvcp-24.4R1.9	3-disk VCP image; all config done here
Core router (forwarding plane)	vMX-VFP / vMX-VFP2	vmxvfp-24.4R1.9	Single-disk VFP; no CLI config, just forwarding
Distribution router	CSR1 / CSR2	Cisco CSR1000v / IOS-XE	
Firewall	Fortinet1 / Fortinet2	FortiGate VM	HA pair
L3 switch	L3-Switch1 / L3-Switch2	Cisco IOL (i86bi_linux_l2-adventerprisek9-ms.SSA.high_iron_20190423)	L2-named image, runs full L3 switching (SVIs, etherchannel) on Catalyst 3750 code
IP Addressing

/30 point-to-point links carved out of 10.1.1.0/27.

Link	Side A	Side B
vMX-VCP ge-0/0/0 ↔ CSR1 Gi1	10.1.1.1/30	10.1.1.2/30
vMX-VCP ge-0/0/1 ↔ CSR2 Gi2	10.1.1.5/30	10.1.1.6/30
vMX-VCP2 ge-0/0/0 ↔ CSR2 Gi1	10.1.1.9/30	10.1.1.10/30
vMX-VCP2 ge-0/0/1 ↔ CSR1 Gi2	10.1.1.13/30	10.1.1.14/30
vMX-VCP ge-0/0/9 ↔ vMX-VCP2 ge-0/0/9	10.1.1.17/30	10.1.1.18/30
