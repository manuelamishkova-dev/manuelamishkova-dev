# Packet Tracer build artifact

The native simulation file belongs in this folder:

```text
routed-lan-dhcp.pkt
```

It is not included yet because a real .pkt file must be saved by Cisco Packet Tracer after creating and validating the topology. No placeholder binary is used.

## Build and save

1. Follow the topology and port map in the [project guide](../README.md).
2. Configure R1, SW1, and SW2 using the text files in [configs](../configs/).
3. Set all four PCs to DHCP.
4. Complete the checks in [verification and troubleshooting](../docs/verification-and-troubleshooting.md).
5. Save the Packet Tracer project here as `routed-lan-dhcp.pkt`.
6. Capture screenshots and place them in the sibling [screenshots](../screenshots/) folder.
7. Update the project status and evidence log only after those steps are complete.

When committing the .pkt file, include the Packet Tracer version used in the project guide if compatibility matters.
