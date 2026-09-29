# IPv4 addressing plan

## Subnet summary

| LAN | Network / prefix | Subnet mask | Usable host range | Broadcast | Gateway |
|---|---|---|---|---|---|
| A | 192.168.10.0/24 | 255.255.255.0 | 192.168.10.1–192.168.10.254 | 192.168.10.255 | 192.168.10.1 |
| B | 192.168.20.0/24 | 255.255.255.0 | 192.168.20.1–192.168.20.254 | 192.168.20.255 | 192.168.20.1 |

Each /24 contains 256 addresses: the network address and directed-broadcast address are not assigned to hosts. The gateway uses .1. The router excludes .1–.20 from dynamic allocation, leaving .21–.254 for DHCP clients or future static addresses. The switch ports operate in their default access VLAN and do not require IP addresses for Layer 2 forwarding.

## Device map

| Device | Port | Peer | Addressing |
|---|---|---|---|
| R1 | G0/0 | SW1 Gi0/1 | 192.168.10.1/24 |
| R1 | G0/1 | SW2 Gi0/1 | 192.168.20.1/24 |
| SW1 | Gi0/1 | R1 G0/0 | No IP required for this Layer 2 forwarding role |
| SW1 | Fa0/2 | PC-A | DHCP from LAN A |
| SW1 | Fa0/3 | PC-B | DHCP from LAN A |
| SW2 | Gi0/1 | R1 G0/1 | No IP required for this Layer 2 forwarding role |
| SW2 | Fa0/2 | PC-C | DHCP from LAN B |
| SW2 | Fa0/3 | PC-D | DHCP from LAN B |

## Why use two separate /24 networks?

A router's two routed interfaces sit in different IPv4 subnets. Each connected interface is a gateway for its local subnet. When a host sends traffic to a destination outside its own subnet, it sends the frame to its default gateway; R1 routes the IP packet toward the other directly connected subnet.

DHCP Discover messages are broadcasts on their local LAN. R1 can serve each subnet because it has an interface in each broadcast domain and a pool for each subnet. No DHCP relay is required in this topology.

## DHCP allocation

R1 advertises these options for each pool:

- Network: the corresponding /24
- Default router: the R1 address on that subnet
- DNS server: not configured (no DNS server is part of this lab)

The first available dynamically allocated address should fall in .21–.254; the exact lease address depends on client requests and the simulator's lease state. Reset a PC's DHCP request or clear the router's DHCP bindings only if you need to repeat an allocation demonstration.
