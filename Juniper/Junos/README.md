# Juniper Junos Operational and Health and Performance Collections

Last updated: 2026-10-24

Tested against: **Juniper Junos - Operational Collection**: not yet validated against a device; **Juniper Junos - Health and Performance Collection**: A virtual Juniper Junos device, apart from the Virtual Chassis monitors

These monitor sets are for: Juniper MX, PTX and ACX routers and EX and QFX switches.

The folder holds two sets; assign both to the same devices:

- **Juniper Junos - Operational Collection** (8 monitors) covers what the device does (its role's state, load and services).
- **Juniper Junos - Health and Performance Collection** (3 monitors) covers the device itself (CPU, memory, storage and hardware).

The collections are a starting point. They define no alert policies or thresholds.

Interface traffic and errors are not included: assign **Standard Network Interfaces - Health and Performance Collection** to the same devices for those.

## Requirements

Before importing the monitor sets, confirm that:

- The device has been added to the ThirdEye inventory.
- ThirdEye can reach the device's SNMP agent, normally over UDP port 161.
- A working SNMP credential has been assigned to the device in ThirdEye. SNMPv3 with authentication and privacy is recommended.
- The MIB modules listed below are loaded in ThirdEye.

### MIB modules

The collections use these modules:

- `BGP4-V2-MIB-JUNIPER`
- `JNX-IPSEC-MONITOR-MIB`
- `JUNIPER-L2ALD-MIB`
- `JUNIPER-MIB`
- `JUNIPER-MPLS-LDP-MIB`
- `JUNIPER-RSVP-MIB`
- `JUNIPER-VIRTUALCHASSIS-MIB`
- `JUNIPER-VLAN-MIB`

ThirdEye's built-in MIB store already includes all of these modules, so no MIB import is needed. The MIB files are not included with these collections; to install or update a module, obtain the files as described below and import them with their dependencies.

### Obtaining the Juniper Networks MIBs

Download the MIB files for the device's software release from Juniper Networks' support site, and import the modules listed above together with their dependencies.

## Installation

1. In **Inventory**, use **Tools > SNMP System Info** to verify that ThirdEye can query each target device.
2. Go to **Monitors > Sets** and import `Juniper Junos - Operational Collection.3em` and `Juniper Junos - Health and Performance Collection.3em`.
3. Assign **Juniper Junos - Operational Collection** and **Juniper Junos - Health and Performance Collection** to the appropriate devices.
4. Wait at least 5 minutes so every monitor has had a collection opportunity, then confirm that current data is present for the applicable monitors.

Automatic assignment to newly discovered devices is disabled in both sets. Review the sets before enabling automatic assignment in your environment.

These host queries match the devices the sets are meant for: `sysObjectID=1.3.6.1.4.1.2636.*`.

## Included monitors

**Juniper Junos - Operational Collection** contains 8 SNMP monitors and **Juniper Junos - Health and Performance Collection** contains 3 SNMP monitors. Metrics carry friendly names; each lists the MIB object it reads or the formula it computes.

### Juniper Junos - Operational Collection

#### Juniper - BGP Peers (1 minute)

Session state of every BGP peer, with its remote AS. Rows are identified by the peer's routing instance and addresses (the table index); there is no name column.

From `BGP4-V2-MIB-JUNIPER::jnxBgpM2PeerTable`.

- `State`: BGP session state (idle, connect, active, opensent, openconfirm, established) (`jnxBgpM2PeerState`).
- `RemoteAs`: The peer's autonomous system number (`jnxBgpM2PeerRemoteAs`).

#### Juniper - VLANs (5 minutes)

Each VLAN's 802.1Q tag and type, labelled by the VLAN name, from the VLAN table of switches running Enhanced Layer 2 Software (ELS: current EX and QFX). Older non-ELS EX switches answer Juniper - VLANs (non-ELS) instead.

From `JUNIPER-L2ALD-MIB::jnxL2aldVlanTable`, one row per `jnxL2aldVlanName`.

- `Tag`: The VLAN's 802.1Q tag (`jnxL2aldVlanTag`).
- `Type`: VLAN type (1 static, 2 dynamic) (`jnxL2aldVlanType`).

#### Juniper - VLANs (non-ELS) (5 minutes)

Each VLAN's 802.1Q tag and type, labelled by the VLAN name, from the VLAN table of older EX switches without Enhanced Layer 2 Software; switches running ELS answer Juniper - VLANs instead.

From `JUNIPER-VLAN-MIB::jnxExVlanTable`, one row per `jnxExVlanName`.

- `Tag`: The VLAN's 802.1Q tag (`jnxExVlanTag`).
- `Type`: VLAN type (1 static, 2 dynamic) (`jnxExVlanType`).

#### Juniper - IKE Tunnels (1 minute)

Each IPsec phase 1 (IKE) tunnel on a router's Multiservices PIC or DPC: negotiation state and local role, the remote gateway and identity, lifetime and time active, and traffic as rates and as volumes since the previous poll. Rows are identified by service set, remote gateway and tunnel index.

From `JNX-IPSEC-MONITOR-MIB::jnxIkeTunnelTable`.

- `State`: Negotiation state (1 matured, 2 notmatured) (`jnxIkeTunNegState`).
- `Role`: This router's role (1 initiator, 2 responder) (`jnxIkeTunLocalRole`).
- `RemoteGateway`: The remote gateway's address (InetAddress) (`jnxIkeTunRemoteGwAddr`).
- `RemoteId`: The remote peer's identity, as an address, FQDN or distinguished name (`jnxIkeTunRemoteIdValue`).
- `LifetimeSecs`: Negotiated lifetime, seconds (`jnxIkeTunLifeTime`).
- `ActiveSecs`: Seconds the tunnel has been active (`$jnxIkeTunActiveTime / 100.0`).
- `BytesIn`: Bytes received since the previous poll (`delta(jnxIkeTunInOctets)`).
- `BytesInPerSec`: Bytes received, per second (`rate(jnxIkeTunInOctets)`).
- `BytesOut`: Bytes sent since the previous poll (`delta(jnxIkeTunOutOctets)`).
- `BytesOutPerSec`: Bytes sent, per second (`rate(jnxIkeTunOutOctets)`).

#### Juniper - IPsec Tunnels (1 minute)

Each IPsec phase 2 tunnel on a router's Multiservices PIC or DPC: its rule, remote gateway, key type and MTU, and traffic decrypted and encrypted as rates and as volumes since the previous poll. Rows are identified by service set, remote gateway and tunnel index.

From `JNX-IPSEC-MONITOR-MIB::jnxIpSecTunnelTable`.

- `Rule`: The IPsec rule the tunnel was configured under (`jnxIpSecRuleName`).
- `RemoteGateway`: The remote gateway's address (InetAddress) (`jnxIpSecTunRemoteGwAddr`).
- `KeyType`: Key type (0 unknown, 1 keyIke, 2 keyManual) (`jnxIpSecTunKeyType`).
- `Mtu`: The tunnel's MTU, bytes (`jnxIpSecTunMtu`).
- `BytesIn`: Bytes decrypted since the previous poll (`delta(jnxIpSecTunInDecryptedBytes)`).
- `BytesInPerSec`: Bytes decrypted, per second (`rate(jnxIpSecTunInDecryptedBytes)`).
- `BytesOut`: Bytes encrypted since the previous poll (`delta(jnxIpSecTunOutEncryptedBytes)`).
- `BytesOutPerSec`: Bytes encrypted, per second (`rate(jnxIpSecTunOutEncryptedBytes)`).

#### Juniper - IPsec Errors (1 minute)

Packets each IPsec phase 2 tunnel dropped or failed, as rates and as counts since the previous poll: anti-replay drops, ESP authentication and decryption failures, bad headers and trailers, and all drops.

From `JNX-IPSEC-MONITOR-MIB::jnxIpSecTunnelTable`.

- `ReplayDrops`: Packets dropped by the anti-replay check since the previous poll (`delta(jnxIpSecTunReplayDropPkts)`).
- `ReplayDropsPerSec`: Packets dropped by the anti-replay check, per second (`rate(jnxIpSecTunReplayDropPkts)`).
- `EspAuthFailures`: Packets that failed ESP authentication since the previous poll (`delta(jnxIpSecTunEspAuthFails)`).
- `EspAuthFailuresPerSec`: Packets that failed ESP authentication, per second (`rate(jnxIpSecTunEspAuthFails)`).
- `DecryptFailures`: Packets that failed decryption since the previous poll (`delta(jnxIpSecTunDecryptFails)`).
- `DecryptFailuresPerSec`: Packets that failed decryption, per second (`rate(jnxIpSecTunDecryptFails)`).
- `BadHeaders`: Packets with bad headers since the previous poll (`delta(jnxIpSecTunBadHeaders)`).
- `BadHeadersPerSec`: Packets with bad headers, per second (`rate(jnxIpSecTunBadHeaders)`).
- `BadTrailers`: Packets with bad ESP trailers since the previous poll (`delta(jnxIpSecTunBadTrailers)`).
- `BadTrailersPerSec`: Packets with bad ESP trailers, per second (`rate(jnxIpSecTunBadTrailers)`).
- `Drops`: Packets dropped for any reason since the previous poll (`delta(jnxIpSecTunDroppedPkts)`).
- `DropsPerSec`: Packets dropped for any reason, per second (`rate(jnxIpSecTunDroppedPkts)`).

#### Juniper - LDP Sessions (1 minute)

Each MPLS LDP session's state, protocol version and keepalive hold time remaining, and when its state last changed. Rows are identified by the local LDP entity and the peer's LDP identifier, since the table has no address column.

From `JUNIPER-MPLS-LDP-MIB::jnxMplsLdpSessionTable`.

- `State`: Session state (1 nonexistent, 2 initialized, 3 openrec, 4 opensent, 5 operational); 5 operational is up (`jnxMplsLdpSesState`).
- `ProtocolVersion`: The LDP protocol version the session negotiated (`jnxMplsLdpSesProtocolVersion`).
- `KeepAliveRemainingSecs`: Seconds of keepalive hold time remaining (`$jnxMplsLdpSesKeepAliveHoldTimeRem / 100.0`).
- `StateLastChange`: sysUpTime when the session state last changed, hundredths of a second (TimeStamp, saved raw) (`jnxMplsLdpSesStateLastChange`).

#### Juniper - RSVP Sessions (5 minutes)

Each RSVP session (LSP): its state, role and path type, source and destination, LSP and tunnel IDs, and MPLS traffic as rates and as volumes since the previous poll, labelled by the session name, which is the LSP name; several sessions can share a name, so rows can share a label. The router updates the traffic counts only every few minutes, so the monitor polls every 5 minutes.

From `JUNIPER-RSVP-MIB::jnxRsvpSessionTable`, one row per `jnxRsvpSessionName`.

- `State`: Session state (1 up, 2 down) (`jnxRsvpSessionState`).
- `Role`: This router's role in the session (1 ingress, 2 transit, 3 egress) (`jnxRsvpSessionRole`).
- `PathType`: Path type, where the head end signals it (1 primary, 2 secondary, 3 unknown) (`jnxRsvpSessionPathType`).
- `From`: Source address (`jnxRsvpSessionFrom`).
- `To`: Destination address (`jnxRsvpSessionTo`).
- `LspId`: The sender's LSP ID (`jnxRsvpSessionLspId`).
- `TunnelId`: Tunnel ID (`jnxRsvpSessionTunnelId`).
- `Bytes`: MPLS bytes forwarded since the previous poll (`delta(jnxRsvpSessionMplsOctets)`).
- `BytesPerSec`: MPLS bytes forwarded, per second (`rate(jnxRsvpSessionMplsOctets)`).
- `Packets`: MPLS packets forwarded since the previous poll (`delta(jnxRsvpSessionMplsPackets)`).
- `PacketsPerSec`: MPLS packets forwarded, per second (`rate(jnxRsvpSessionMplsPackets)`).

### Juniper Junos - Health and Performance Collection

#### Juniper - Component Health (1 minute)

Per chassis component (routing engines, FPCs, PICs, fans, power supplies): state, temperature, CPU and memory use. Values the component does not have read 0.

From `JUNIPER-MIB::jnxOperatingTable`, one row per `jnxOperatingDescr`.
Covers the TES built-in templates *Juniper Junos - CPU Utilization*, *Juniper Junos - Memory Utilization*: it polls every object they poll, under its own names.

- `State`: Operating state (unknown, running, ready, reset, runningAtFullSpeed, down, standby) (`jnxOperatingState`).
- `TemperatureC`: Temperature, degrees Celsius (`jnxOperatingTemp`).
- `CpuPct`: CPU utilization, percent (`jnxOperatingCPU`).
- `Cpu5MinPct`: CPU utilization averaged over the last 5 minutes, percent (`jnxOperating5MinAvgCPU`).
- `BufferPct`: Buffer pool utilization, percent (`jnxOperatingBuffer`).
- `HeapPct`: Heap utilization, percent (`jnxOperatingHeap`).

#### Juniper - Virtual Chassis Members (1 minute)

Each Virtual Chassis member's role and priority, model, serial number, software version and uptime. Rows are identified by member ID.

From `JUNIPER-VIRTUALCHASSIS-MIB::jnxVirtualChassisMemberTable`.

- `Role`: Role in the Virtual Chassis (1 master, 2 backup, 3 linecard) (`jnxVirtualChassisMemberRole`).
- `Priority`: Mastership priority, 0 to 255 (`jnxVirtualChassisMemberPriority`).
- `Model`: The member's model (`jnxVirtualChassisMemberModel`).
- `SerialNumber`: The member's serial number (`jnxVirtualChassisMemberSerialnumber`).
- `SoftwareVersion`: The Junos version the member runs (`jnxVirtualChassisMemberSWVersion`).
- `Uptime`: The member's uptime, as the switch reports it (the MIB gives no unit) (`jnxVirtualChassisMemberUptime`).

#### Juniper - Virtual Chassis Ports (1 minute)

Each Virtual Chassis port's administrative and operational state, traffic as rates and as volumes since the previous poll, and carrier transitions and CRC errors as rates and as counts, labelled by the port name. Rows are identified by member (FPC) and port name.

From `JUNIPER-VIRTUALCHASSIS-MIB::jnxVirtualChassisPortTable`, one row per `jnxVirtualChassisPortName`.

- `AdminStatus`: Administrative state (1 up, 2 down, 3 unknown) (`jnxVirtualChassisPortAdminStatus`).
- `OperStatus`: Operational state (1 up, 2 down, 3 unknown) (`jnxVirtualChassisPortOperStatus`).
- `BytesIn`: Bytes received since the previous poll (`delta(jnxVirtualChassisPortInOctets)`).
- `BytesInPerSec`: Bytes received, per second (`rate(jnxVirtualChassisPortInOctets)`).
- `BytesOut`: Bytes sent since the previous poll (`delta(jnxVirtualChassisPortOutOctets)`).
- `BytesOutPerSec`: Bytes sent, per second (`rate(jnxVirtualChassisPortOutOctets)`).
- `CarrierTransitions`: Carrier transitions since the previous poll (`delta(jnxVirtualChassisPortCarrierTrans)`).
- `CarrierTransitionsPerSec`: Carrier transitions, per second (`rate(jnxVirtualChassisPortCarrierTrans)`).
- `CrcErrors`: Input CRC and alignment errors since the previous poll (`delta(jnxVirtualChassisPortInCRCAlignErrors)`).
- `CrcErrorsPerSec`: Input CRC and alignment errors, per second (`rate(jnxVirtualChassisPortInCRCAlignErrors)`).

`delta(x)` is the change in counter `x` between the last two polls; `rate(x)` is that change divided by the monitor's polling period in seconds (TES's `$pollingSecs`), a per-second rate.

## Expected variations

A table monitor returns no rows when the device lacks the feature or hardware it covers. An empty table does not necessarily indicate a collection failure: first confirm that the feature is present and configured on the device.

- The VLAN, IPsec, IKE, LDP, RSVP and BGP monitors return no rows on a device where the feature is not configured; Juniper - BGP Peers returned no rows on a virtual Junos device with no BGP sessions.
- Juniper - VLANs reads the VLAN table of switches running Enhanced Layer 2 Software (current EX and QFX), Juniper - VLANs (non-ELS) that of older EX switches; a switch is expected to answer one of them.
- Juniper's documentation gives the IPsec Monitoring MIB that Juniper - IKE Tunnels, Juniper - IPsec Tunnels and Juniper - IPsec Errors poll as supported on Multiservices PICs and DPCs in M Series and MX Series routers; SRX firewalls use a different IPsec MIB, which these monitors do not poll.
- Juniper - RSVP Sessions' traffic counts are updated only at the router's MPLS statistics interval, so its rates move in steps.
- Rows of the IKE, IPsec and LDP monitors are identified by index values (service set names, addresses and LDP identifiers), which ThirdEye can show as OID digits, since those tables have no name column.

## Validation

To run the **SNMP System Info** tool, open **Inventory**, select the target device, open **Tools**, and select **SNMP System Info**. A successful test returns basic system information, including the system description and uptime, rather than an SNMP timeout or authentication error.

After installation:

1. Run **SNMP System Info** for every assigned device and confirm that it succeeds.
2. Confirm that the one-minute monitors have current samples.
3. Verify that each applicable table monitor returns the expected rows.
4. Compare values with the device's own interface or CLI before creating alert thresholds.

## Limitations

- The sets perform collection only; they do not install alert policies, notification destinations, or incident workflows.
- Counts are changes between polls and rates are per second; alert on those rather than on raw lifetime counters.
- The sets contain no SNMP usernames, secrets, device addresses or other environment-specific values.

## Troubleshooting

If no data is collected:

1. Run **Inventory > Tools > SNMP System Info** against the device.
2. Confirm network reachability from ThirdEye to UDP port 161 on the device.
3. Verify the device's SNMP configuration and the credential assigned in ThirdEye.
4. Confirm that the MIB modules listed under Requirements are present in ThirdEye.
5. Wait for the applicable collection interval after making a correction.

If scalar metrics work but a table monitor is empty, confirm that the corresponding feature is configured before treating the result as an error.
