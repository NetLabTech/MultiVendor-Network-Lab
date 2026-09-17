
# Multi-Vendor Network Lab (EVE-NG)

Multi-vendor lab environment covering routing, switching, and security design across Juniper, Cisco, and Fortinet platforms, built on bare-metal EVE-NG.

## Scope

- Cross-vendor interop and troubleshooting (Juniper / Cisco / Fortinet)
- Core → distribution → security → access design
- Junos (JNCIS-ENT / JNCIP-ENT) alongside Cisco IOS-XE(CCNP)
- FortiGate HA pair with dedicated sync link

## Topology

![Topology Diagram](https://github.com/NetLabTech/MultiVendor-Network-Lab/blob/4779d04c2c36d1d10e0cc6bd18cbbd917a548e74/typology_diagram.png) 


**Design:**
- **Core (Juniper vMX pair):** VCP/VFP pair, full mesh to both CSRs
- **Distribution (Cisco CSR1000v pair):** dual-homed to both vMX nodes, mesh + dynamic routing for failover
- **Security perimeter (FortiGate pair):** 1:1 links only, dedicated HA sync link between the two
- **Access/distribution switching (Cisco IOL L3):** one per firewall, 1:1, VLANs/SVIs to follow

## Devices & Images

| Role | Device | Image | Notes |
|---|---|---|---|
| Core router (control plane) | vMX-VCP / vMX-VCP2 | vmxvcp-24.4R1.9 | 3-disk VCP image; all config done here |
| Core router (forwarding plane) | vMX-VFP / vMX-VFP2 | vmxvfp-24.4R1.9 | Single-disk VFP; no CLI config, just forwarding |
| Distribution router | CSR1 / CSR2 | Cisco CSR1000v / IOS-XE | |
| Firewall | Fortinet1 / Fortinet2 | FortiGate VM | HA pair |
| L3 switch | L3-Switch1 / L3-Switch2 | Cisco IOL (i86bi_linux_l2-adventerprisek9-ms.SSA.high_iron_20190423) | L2-named image, runs full L3 switching (SVIs, etherchannel) on Catalyst 3750 code |

## IP Addressing

`/30` point-to-point links carved out of `10.1.1.0/27`.

| Link | Side A | Side B |
|---|---|---|
| vMX-VCP ge-0/0/0 ↔ CSR1 Gi1 | 10.1.1.1/30 | 10.1.1.2/30 |
| vMX-VCP ge-0/0/1 ↔ CSR2 Gi2 | 10.1.1.5/30 | 10.1.1.6/30 |
| vMX-VCP2 ge-0/0/0 ↔ CSR2 Gi1 | 10.1.1.9/30 | 10.1.1.10/30 |
| vMX-VCP2 ge-0/0/1 ↔ CSR1 Gi2 | 10.1.1.13/30 | 10.1.1.14/30 |
| vMX-VCP ge-0/0/9 ↔ vMX-VCP2 ge-0/0/9 | 10.1.1.17/30 | 10.1.1.18/30 |

| CSR1 Gi3 ↔ Fortinet1 port1 | 10.1.1.21/30 | 10.1.1.22/30 |
| CSR2 Gi3 ↔ Fortinet2 port1 | 10.1.1.25/30 | 10.1.1.26/30 |
| Fortinet1 port2 ↔ Fortinet2 port2 (HA sync) | 10.1.1.29/30 | 10.1.1.30/30 |
| Fortinet1 port3 ↔ CiscoSwitch1L3 e0/0 | 10.1.1.33/30 | 10.1.1.34/30 |
| Fortinet2 port3 ↔ CiscoSwitch2L3 e0/0 | 10.1.1.37/30 | 10.1.1.38/30 |

*(Block has grown beyond the original `/27` — effectively `10.1.1.0/26` now. Flag for summarization if this gets advertised.)*

## vMX Wiring

VCP/VFP internal links must be wired before any `ge-` interfaces appear:

- VCP `em0` (fxp0) ↔ VFP `em0` (fxp0) — mgmt/control
- VCP `em1` (internal) ↔ VFP `em1` (internal) — control-to-forwarding
- VCP `em2` onward ↔ maps to `ge-0/0/0`, `ge-0/0/1`, etc.

```
root> show chassis fpc
```
Slot 0 must show `State: Online` before configuring. All config lives on the VCP; the VFP console is a bare Linux login and stays untouched.

## Troubleshooting Log

- **vJunosEvo ↔ Cisco vIOS: 0% ping both directions**, correct addressing, interfaces up/up. ARP never completed either side. Ruled out NIC type (virtio vs e1000 — matched both ends, no change). Isolated to vJunosEvo's dataplane rather than the host/bridge setup, since vMX ↔ CSR1000v on the same host works cleanly.
- **vMX ↔ CSR1000v: clean.** Junos-to-Junos direct link (VCP ↔ VCP2) at 100% ping, confirming the host bridge setup. Cisco IOL L3 switching (SVIs, etherchannel) confirmed stable on `SSA.high_iron_20190423`; older L2-Adv builds have known bugs with switchport access mode + L3 etherchannel.
- **NIC type changes require a full stop/start of both link ends** — qemu-level setting, not guest-OS reload. IOSv breaks hard (empty `show ip int br`, unrecoverable) if changed live; recovery required reverting NIC type and/or EVE-NG "Wipe."


## Roadmap

- [x] Cable and provision the security perimeter (Fortinet ↔ CSR, Fortinet ↔ L3-Switch, 1:1)
- [ ] Apply addressing to the new Fortinet/CSR-Gi3/switch links and confirm end-to-end reachability
- [ ] Bring up FortiGate HA cleanly and force failover under load to validate the sync link
- [ ] Cut over from static to OSPF across the vMX ↔ CSR mesh; confirm convergence and no black-holing during the transition
- [ ] Build out VLANs/SVIs on the L3 switches and confirm inter-VLAN routing before layering ACLs/security policy on top
- [ ]  Introduce eBGP between the vMX pair and CSR pair (replace/augment OSPF at that boundary), practice route filtering and path selection
- [ ] Redistribute between OSPF and BGP at the CSR boundary; test for routing loops
- [ ] Apply route-maps / prefix-lists for policy-based routing between security zones
- [ ] Basic QoS policy on the CSR pair (classification/marking) ahead of the firewall links
- [ ] Add a cloud/NAT object above the vMX pair for external NTP/DNS reachability
- [ ] Document baseline configs and export running configs for change control / rollback
- [ ] 

  


  




