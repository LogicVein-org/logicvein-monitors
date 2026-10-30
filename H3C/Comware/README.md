# H3C Comware Device Operational and Health and Performance Collections

Last updated: 2026-10-30

Tested against: Not yet validated against a device.

These monitor sets are for: H3C and HPE Comware switches and routers.

The folder holds two sets; assign both to the same devices:

- **H3C Comware Device - Operational Collection** (8 monitors) covers what the device does (its role's state, load and services).
- **H3C Comware Device - Health and Performance Collection** (3 monitors) covers the device itself (CPU, memory, storage and hardware).

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

- `ENTITY-MIB`
- `H3C-ENTITY-EXT-MIB`
- `HH3C-ENTITY-EXT-MIB`
- `HH3C-IKE-MONITOR-MIB`
- `HH3C-IPSEC-MONITOR-MIB`
- `HH3C-LswVLAN-MIB`
- `HH3C-RRPP-MIB`
- `HH3C-STACK-MIB`

ThirdEye's built-in MIB store already includes all of these modules, so no MIB import is needed. The MIB files are not included with these collections; to install or update a module, obtain the files as described below and import them with their dependencies.

### Obtaining the H3C MIBs

Download the MIB files for the device's software release from H3C's support site, and import the modules listed above together with their dependencies.

## Installation

1. In **Inventory**, use **Tools > SNMP System Info** to verify that ThirdEye can query each target device.
2. Go to **Monitors > Sets** and import `H3C Comware Device - Operational Collection.3em` and `H3C Comware Device - Health and Performance Collection.3em`.
3. Assign **H3C Comware Device - Operational Collection** and **H3C Comware Device - Health and Performance Collection** to the appropriate devices.
4. Wait at least 5 minutes so every monitor has had a collection opportunity, then confirm that current data is present for the applicable monitors.

Automatic assignment to newly discovered devices is disabled in both sets. Review the sets before enabling automatic assignment in your environment.

These host queries match the devices the sets are meant for: `sysObjectID=1.3.6.1.4.1.25506.*`, `sysObjectID=1.3.6.1.4.1.43.*`.

## Included monitors

**H3C Comware Device - Operational Collection** contains 8 SNMP monitors and **H3C Comware Device - Health and Performance Collection** contains 3 SNMP monitors. Metrics carry friendly names; each lists the MIB object it reads or the formula it computes.

### H3C Comware Device - Operational Collection

#### H3C - IPsec Tunnels (5 minutes)

Each IPsec phase 2 tunnel on a Comware router: its status, local and remote addresses, key type, how long it has been active and its SA's remaining time, labelled by the IPsec policy name. Rows are identified by the interface's ifIndex, the IPsec policy and the tunnel.

From `HH3C-IPSEC-MONITOR-MIB::hh3cIPSecTunnelTable`, one row per `hh3cIPSecTunPolicyName`.

- `Status`: Tunnel status (1 initial, 2 ready, 3 rekeyed, 4 closed) (`hh3cIPSecTunStatus`).
- `LocalAddr`: Local endpoint address (`hh3cIPSecTunLocalAddr`).
- `RemoteAddr`: Remote endpoint address (`hh3cIPSecTunRemoteAddr`).
- `KeyType`: Key type (1 ike, 2 manual; 2147483647 invalid) (`hh3cIPSecTunKeyType`).
- `ActiveSecs`: Seconds the tunnel has been active, from hundredths of a second; the invalid value 2147483647 reads 21474836.47 (`$hh3cIPSecTunActiveTime / 100.0`).
- `RemainSecs`: The SA's remaining time, seconds; 2147483647 means invalid (`hh3cIPSecTunRemainTime`).

#### H3C - IPsec Traffic (5 minutes)

Each IPsec phase 2 tunnel's traffic in each direction and packets dropped, as rates and as volumes since the previous poll. Rows are identified by the interface's ifIndex, the IPsec policy and the tunnel.

From `HH3C-IPSEC-MONITOR-MIB::hh3cIPSecTunnelStatTable`.

- `BytesIn`: Bytes received since the previous poll (`delta(hh3cIPSecTunInOctets)`).
- `BytesInPerSec`: Bytes received, per second (`rate(hh3cIPSecTunInOctets)`).
- `BytesOut`: Bytes sent since the previous poll (`delta(hh3cIPSecTunOutOctets)`).
- `BytesOutPerSec`: Bytes sent, per second (`rate(hh3cIPSecTunOutOctets)`).
- `DropsIn`: Inbound packets dropped since the previous poll (`delta(hh3cIPSecTunInDropPkts)`).
- `DropsInPerSec`: Inbound packets dropped, per second (`rate(hh3cIPSecTunInDropPkts)`).
- `DropsOut`: Outbound packets dropped since the previous poll (`delta(hh3cIPSecTunOutDropPkts)`).
- `DropsOutPerSec`: Outbound packets dropped, per second (`rate(hh3cIPSecTunOutDropPkts)`).

#### H3C - IPsec Errors (5 minutes)

Each IPsec phase 2 tunnel's anti-replay drops and authentication, decryption and encryption failures, as rates and as counts since the previous poll. Rows are identified by the interface's ifIndex, the IPsec policy and the tunnel.

From `HH3C-IPSEC-MONITOR-MIB::hh3cIPSecTunnelStatTable`.

- `ReplayDrops`: Packets dropped by the anti-replay check since the previous poll (`delta(hh3cIPSecTunInReplayDropPkts)`).
- `ReplayDropsPerSec`: Packets dropped by the anti-replay check, per second (`rate(hh3cIPSecTunInReplayDropPkts)`).
- `AuthFailures`: Inbound packets that failed authentication since the previous poll (`delta(hh3cIPSecTunInAuthFails)`).
- `AuthFailuresPerSec`: Inbound packets that failed authentication, per second (`rate(hh3cIPSecTunInAuthFails)`).
- `DecryptFailures`: Inbound packets that failed decryption since the previous poll (`delta(hh3cIPSecTunInDecryptFails)`).
- `DecryptFailuresPerSec`: Inbound packets that failed decryption, per second (`rate(hh3cIPSecTunInDecryptFails)`).
- `EncryptFailures`: Outbound packets that failed encryption since the previous poll (`delta(hh3cIPSecTunOutEncryptFails)`).
- `EncryptFailuresPerSec`: Outbound packets that failed encryption, per second (`rate(hh3cIPSecTunOutEncryptFails)`).

#### H3C - IKE Tunnels (5 minutes)

Each IKE (phase 1) tunnel: its state, remote address and identity, who initiated it, the negotiation mode, and its lifetime, time active and remaining time. Rows are identified by tunnel index.

From `HH3C-IKE-MONITOR-MIB::hh3cIKETunnelTable`.

- `State`: Tunnel state (1 active, 2 timeout) (`hh3cIKETunState`).
- `RemoteAddr`: Remote endpoint address (`hh3cIKETunRemoteAddr`).
- `RemoteId`: The remote peer's identity (`hh3cIKETunRemoteValue1`).
- `Initiator`: Who initiated the tunnel (1 local, 2 remote) (`hh3cIKETunInitiator`).
- `NegotiationMode`: Negotiation mode (2 mainMode, 4 aggressiveMode, 32 quickMode) (`hh3cIKETunNegoMode`).
- `LifetimeSecs`: Negotiated lifetime, seconds (`hh3cIKETunLifeTime`).
- `ActiveSecs`: Seconds the tunnel has been active (`hh3cIKETunActiveTime`).
- `RemainSecs`: The SA's remaining time, seconds (`hh3cIKETunRemainTime`).

#### H3C - IKE Traffic (5 minutes)

Each IKE (phase 1) tunnel's traffic in each direction and packets dropped, as rates and as volumes since the previous poll. Rows are identified by tunnel index.

From `HH3C-IKE-MONITOR-MIB::hh3cIKETunnelStatTable`.

- `BytesIn`: Bytes received since the previous poll (`delta(hh3cIKETunInOctets)`).
- `BytesInPerSec`: Bytes received, per second (`rate(hh3cIKETunInOctets)`).
- `BytesOut`: Bytes sent since the previous poll (`delta(hh3cIKETunOutOctets)`).
- `BytesOutPerSec`: Bytes sent, per second (`rate(hh3cIKETunOutOctets)`).
- `DropsIn`: Inbound packets dropped since the previous poll (`delta(hh3cIKETunInDropPkts)`).
- `DropsInPerSec`: Inbound packets dropped, per second (`rate(hh3cIKETunInDropPkts)`).
- `DropsOut`: Outbound packets dropped since the previous poll (`delta(hh3cIKETunOutDropPkts)`).
- `DropsOutPerSec`: Outbound packets dropped, per second (`rate(hh3cIKETunOutDropPkts)`).

#### H3C - RRPP Rings (5 minutes)

Each Rapid Ring Protection Protocol (RRPP) ring: whether it is enabled and active, its state, this switch's node mode, the ring level and its primary and secondary ports. Rows are identified by domain and ring.

From `HH3C-RRPP-MIB::hh3cRrppRingTable`.

- `Enabled`: Whether RRPP is enabled on the ring (1 enabled, 2 disabled) (`hh3cRrppRingEnableStatus`).
- `Active`: Whether the ring is active (1 active, 2 inactive) (`hh3cRrppRingActive`).
- `State`: Ring state (1 unknown, 2 health, 3 fault) (`hh3cRrppRingState`).
- `NodeMode`: This switch's node mode (1 master, 2 transit, 3 edge, 4 assistantEdge) (`hh3cRrppRingNodeMode`).
- `Level`: Ring level (1 majorRing, 2 subRing) (`hh3cRrppRingLevel`).
- `PrimaryPort`: The primary (or common) port (`hh3cRrppRingPrimaryPort`).
- `SecondaryPort`: The secondary (or edge) port (`hh3cRrppRingSecondaryPort`).

#### H3C - RRPP Ports (5 minutes)

Each RRPP ring port's role and state, and illegal RRPP packets received, as a rate and as a count since the previous poll.

From `HH3C-RRPP-MIB::hh3cRrppPortTable`.

- `Role`: The port's RRPP role (1 primary, 2 secondary, 3 common, 4 edge) (`hh3cRrppPortRole`).
- `State`: Port state (1 unknown, 2 unblocked, 3 blocked, 4 down) (`hh3cRrppPortState`).
- `RxErrors`: Illegal RRPP packets received since the previous poll (`delta(hh3cRrppPortRXError)`).
- `RxErrorsPerSec`: Illegal RRPP packets received, per second (`rate(hh3cRrppPortRXError)`).

#### H3C - VLANs (5 minutes)

Each VLAN's type, how it was created, and whether it has a VLAN interface, labelled by the VLAN name.

From `HH3C-LswVLAN-MIB::hh3cdot1qVlanMIBTable`, one row per `hh3cdot1qVlanName`.

- `Type`: VLAN type (1 superVlan, 2 common-vlan, 3 sub-vlan, 4 isolate-user-vlan, 5 secondary-vlan) (`hh3cdot1qVlanType`).
- `CreatedBy`: How the VLAN was created (1 other, 2 static, 3 dynamic) (`hh3cdot1qVlanStatus`).
- `HasInterface`: Whether a VLAN interface exists (TruthValue: 1 true, 2 false) (`hh3cExistInterface`).
- `InterfaceVlanId`: The VLAN ID when a VLAN interface is configured, else 0 (not an ifIndex) (`hh3cVlanInterfaceIndex`).

### H3C Comware Device - Health and Performance Collection

#### H3C - Board Health (5 minutes)

CPU and memory use, temperature and fault state per board or module (HH3C-ENTITY-EXT-MIB, current Comware releases).

From `HH3C-ENTITY-EXT-MIB::hh3cEntityExtStateTable`, one row per `entPhysicalName`.

- `CpuPct`: CPU usage of the entity, percent (`hh3cEntityExtCpuUsage`).
- `MemoryPct`: Memory used, percent (`hh3cEntityExtMemUsage`).
- `Temperature`: Temperature as the entity reports it (the MIB does not state the unit) (`hh3cEntityExtTemperature`).
- `ErrorStatus`: Fault state (normal, fanError, psuError, sensorError, hardwareFaulty and others) (`hh3cEntityExtErrorStatus`).

#### H3C - Board Health (older Comware) (5 minutes)

The same per-board values from H3C-ENTITY-EXT-MIB, which older Comware releases answer instead.

From `H3C-ENTITY-EXT-MIB::h3cEntityExtStateTable`, one row per `entPhysicalName`.

- `CpuPct`: CPU usage of the entity, percent (`h3cEntityExtCpuUsage`).
- `MemoryPct`: Memory used, percent (`h3cEntityExtMemUsage`).
- `Temperature`: Temperature as the entity reports it (the MIB does not state the unit) (`h3cEntityExtTemperature`).
- `ErrorStatus`: Fault state (normal, fanError, psuError, sensorError, hardwareFaulty and others) (`h3cEntityExtErrorStatus`).

#### H3C - IRF Ports (5 minutes)

Each IRF (stack) port's state, whether physical ports are bound to it, and the member on its far end. Rows are identified by member and port.

From `HH3C-STACK-MIB::hh3cStackPortInfoTable`.

- `Status`: Link state (1 up, 2 down, 3 silent, 4 disabled) (`hh3cStackPortStatus`).
- `PortsBound`: Whether physical ports are bound to the IRF port (1 disabled: none bound, 2 enabled: some bound); not an administrative enable (`hh3cStackPortEnable`).
- `Neighbor`: Member ID of the port's neighbour; 0 for none (`hh3cStackNeighbor`).

`delta(x)` is the change in counter `x` between the last two polls; `rate(x)` is that change divided by the monitor's polling period in seconds (TES's `$pollingSecs`), a per-second rate.

## Expected variations

A table monitor returns no rows when the device lacks the feature or hardware it covers. An empty table does not necessarily indicate a collection failure: first confirm that the feature is present and configured on the device.

- The IPsec, IKE, RRPP and VLAN monitors return no rows on a device where the feature is not configured.
- IPsec values of 2147483647 mean invalid; H3C - IPsec Tunnels' ActiveSecs then reads 21474836.47.
- Rows of the IPsec, IKE and RRPP tables are identified by index values (ifIndex, policy and tunnel numbers, domain and ring), which ThirdEye can show as OID digits.

## Validation

To run the **SNMP System Info** tool, open **Inventory**, select the target device, open **Tools**, and select **SNMP System Info**. A successful test returns basic system information, including the system description and uptime, rather than an SNMP timeout or authentication error.

After installation:

1. Run **SNMP System Info** for every assigned device and confirm that it succeeds.
2. Confirm that the 5-minute monitors have current samples.
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
