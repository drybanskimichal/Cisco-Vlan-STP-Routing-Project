# Design Notes

## Physical Topology (Phase 1)
Two core switches provide redundant uplinks to all three access switches, and the
router connects only to SW-CORE1. This creates intentional Layer 2 loops so that
Rapid Spanning Tree Protocol has something to resolve.

## Router Uplink (Phase 1)
R1 connects only to SW-CORE1. A second physical link to SW-CORE2 was considered for
full router redundancy, but that requires a first-hop redundancy protocol (HSRP/VRRP),
which is outside current course scope. Planned as a future improvement.

## VLAN & Subnetting (Phase 3)
Used VLSM to size subnets according to expected department needs — Sales gets the
largest block (/26), IT and Finance get /27 each, and a small /28 is reserved for
switch management (VLAN 99).

## Trunking (Phase 4)
Trunk ports carry only the VLANs actually needed on each link — for example, the
trunk to SW-ACC3 only allows VLANs 30 and 99, since Finance PCs don't need to see
Sales or IT broadcast traffic. Core-to-core and core-to-router trunks allow all VLANs,
since the router needs Layer 3 reachability to every VLAN.

## STP/RSTP Root Bridge (Phase 5)
SW-CORE1 was set as the primary root bridge (priority 4096) for all VLANs, since it's
the switch directly connected to the router — keeping the shortest path to the routed
uplink through the root minimizes suboptimal forwarding paths. SW-CORE2 was set as the
secondary root (priority 8192) to take over automatically if SW-CORE1 fails.
Verified with 'show spanning-tree': SW-CORE1 shows as root, and redundant links on
access switches show a port in Blocking state as expected.
