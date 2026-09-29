# Verification and troubleshooting

Run the checks after cabling and configuration. Record actual observations in the evidence table; the expected conditions below are not claimed test results.

## 1. Check physical and interface state

On R1:

```text
show ip interface brief
show interfaces gigabitEthernet0/0
show interfaces gigabitEthernet0/1
```

Expected condition: G0/0 and G0/1 have the planned addresses and show up/up after their links are connected and enabled.

On SW1 and SW2:

```text
show interfaces status
```

Expected condition: the router uplink and connected PC access ports are connected. A port with no cable attached can show notconnect.

If a router interface is administratively down, enter the interface configuration and use `no shutdown`. If a link remains down, inspect the selected ports, cable, and peer interface.

## 2. Check DHCP

On each PC, open **Desktop → Command Prompt** and run:

```text
ipconfig
```

Expected condition:

- PC-A and PC-B have addresses in 192.168.10.21–192.168.10.254, mask 255.255.255.0, and gateway 192.168.10.1.
- PC-C and PC-D have addresses in 192.168.20.21–192.168.20.254, mask 255.255.255.0, and gateway 192.168.20.1.

On R1:

```text
show ip dhcp pool
show ip dhcp binding
```

Expected condition: each pool has addresses available and the binding table contains the active client leases after the PCs request DHCP.

If a PC has an automatic 169.254.x.x address or no lease, verify the PC is set to DHCP, the switch port has link, the connected router interface is up, and the matching pool's network and default-router values are correct. Renew the DHCP request in the PC's IP Configuration window.

## 3. Check routing and reachability

On R1:

```text
show ip route
```

Expected condition: the routing table includes connected routes for 192.168.10.0/24 on G0/0 and 192.168.20.0/24 on G0/1. These routes are installed automatically when the interfaces are up and addressed.

From each PC, ping its gateway first. Then test same-LAN and cross-LAN traffic:

```text
ping 192.168.10.1
ping <same-LAN-PC-address>
ping <remote-LAN-PC-address>
```

Use the actual lease addresses shown by `ipconfig`. A first ping may time out while ARP resolves; repeat it once before diagnosing failure.

## 4. Check Layer 2 MAC learning

On SW1:

```text
show mac address-table dynamic
```

Expected condition after traffic has passed: the switch learns the client MAC addresses on Fa0/2 and Fa0/3. It can also learn R1's MAC on Gi0/1. SW2 should show the equivalent LAN B ports. The exact table depends on recent traffic and address aging.

A switch learns a source MAC address from received Ethernet frames. It forwards known unicast frames toward the learned port and floods an unknown unicast within that VLAN until it learns the destination.

## Troubleshooting cases

| Symptom | Check | Likely repair |
|---|---|---|
| Router port shows administratively down | `show ip interface brief` | Enter the correct interface and issue `no shutdown`. |
| Link stays down | Interface IDs, cable, and peer port | Connect the documented ports; check both ends and wait for link negotiation. |
| PC gets 169.254.x.x or no address | PC DHCP setting, switch link, router interface, pool | Select DHCP; restore link; confirm the correct pool network and router option. |
| PC has a lease but cannot ping local gateway | PC mask/gateway, router interface address, access port | Make the mask /24 and gateway match its LAN; verify the router interface and cabling. |
| Same-LAN ping fails but gateway works | PC addresses, peer PC power/firewall state, switch MAC table | Confirm both PCs are in the same /24, powered on, and attached to the intended switch. |
| Local pings work but cross-LAN ping fails | Both router interfaces and `show ip route` | Verify each routed interface is up/up and addressed in its own subnet; both connected routes should appear automatically. |
| Wrong DHCP gateway is handed out | `show running-config` and PC `ipconfig` | Correct that pool's `default-router`, then renew the PC's lease. |
| DHCP pool has no free addresses | `show ip dhcp pool`, `show ip dhcp binding` | Inspect scope size and stale leases; this four-PC lab should have ample capacity. |

## Suggested fault-injection practice

Make one change at a time, note the symptom and the command that exposed it, then restore the intended configuration and rerun the relevant checks.

1. Shut down R1 G0/0. Observe LAN A's link and DHCP behavior; restore with `no shutdown`.
2. Temporarily give one PC in LAN A a wrong default gateway. Compare local-subnet reachability with cross-subnet reachability; restore DHCP.
3. Temporarily change a DHCP pool's `default-router` to the wrong address. Renew a client lease, observe the result, fix the pool, and renew again.
4. Disconnect a PC cable and inspect `show interfaces status` and the learned MAC table; reconnect it and generate traffic.

Do not save an intentionally broken state as the final project.

## Evidence log

Fill this table only after running the checks in Packet Tracer.

| Check | Observed result | Screenshot / note |
|---|---|---|
| Router interfaces | Pending Packet Tracer run | |
| DHCP leases on all four PCs | Pending Packet Tracer run | |
| Local gateway and same-LAN pings | Pending Packet Tracer run | |
| Cross-LAN ping | Pending Packet Tracer run | |
| Connected routes on R1 | Pending Packet Tracer run | |
| Dynamic MAC learning on each switch | Pending Packet Tracer run | |
