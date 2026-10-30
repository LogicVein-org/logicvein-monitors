# Extreme VOSS Switch Operational and Health and Performance Collections

Last updated: 2026-10-30

Tested against: **Extreme VOSS Switch - Operational Collection**: not yet validated against a device; **Extreme VOSS Switch - Health and Performance Collection**: A virtual Extreme VSP running VOSS

These monitor sets are for: Extreme Networks VSP switches on VOSS (Fabric Engine).

The folder holds two sets; assign both to the same devices:

- **Extreme VOSS Switch - Operational Collection** (6 monitors) covers what the device does (its role's state, load and services).
- **Extreme VOSS Switch - Health and Performance Collection** (3 monitors) covers the device itself (CPU, memory, storage and hardware).

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

- `IF-MIB`
- `ISIS-MIB`
- `RAPID-CITY`

ThirdEye's built-in MIB store already includes all of these modules, so no MIB import is needed. The MIB files are not included with these collections; to install or update a module, obtain the files as described below and import them with their dependencies.

### Obtaining the Extreme Networks MIBs

Download the MIB files for the device's software release from Extreme Networks' support site, and import the modules listed above together with their dependencies.

## Installation

1. In **Inventory**, use **Tools > SNMP System Info** to verify that ThirdEye can query each target device.
2. Go to **Monitors > Sets** and import `Extreme VOSS Switch - Operational Collection.3em` and `Extreme VOSS Switch - Health and Performance Collection.3em`.
3. Assign **Extreme VOSS Switch - Operational Collection** and **Extreme VOSS Switch - Health and Performance Collection** to the appropriate devices.
4. Wait at least 5 minutes so every monitor has had a collection opportunity, then confirm that current data is present for the applicable monitors.

Automatic assignment to newly discovered devices is disabled in both sets. Review the sets before enabling automatic assignment in your environment.

These host queries match the devices the sets are meant for: `sysObjectID=1.3.6.1.4.1.2272.*`.

## Included monitors

**Extreme VOSS Switch - Operational Collection** contains 6 SNMP monitors and **Extreme VOSS Switch - Health and Performance Collection** contains 3 SNMP monitors. Metrics carry friendly names; each lists the MIB object it reads or the formula it computes.

### Extreme VOSS Switch - Operational Collection

#### VOSS - VLANs (5 minutes)

Each VLAN's type, the I-SID it maps to in an SPB fabric, its spanning tree group, whether it routes, and its VRF, labelled by the VLAN name.

From `RAPID-CITY::rcVlanTable`, one row per `rcVlanName`.

- `Type`: VLAN type (1 byPort, 2 byIpSubnet, 3 byProtocolId, 4 bySrcMac, 5 byDstMcast, 6 bySvlan, 7 byIds, 8 byPortIp, 9 byPortEvpn, 10 byPortDefault, 11 spbm-bvlan, 12 spbm-switchedUni, 13 private) (`rcVlanType`).
- `Isid`: The I-SID the VLAN maps to in the SPB fabric (0 for none) (`rcVlanPlsbIsid`).
- `StgId`: The spanning tree group the VLAN belongs to (`rcVlanStgId`).
- `RoutingEnabled`: Whether the VLAN routes (TruthValue: 1 true, 2 false) (`rcVlanRoutingEnable`).
- `Vrf`: The VRF the VLAN belongs to (`rcVlanVrfName`).

#### VOSS - MLTs (5 minutes)

Each multi-link trunk (MLT): its configured and running type (normal, IST, split or virtual IST), split MLT ID, whether it is enabled, its link aggregation state and minimum links, and when that state last changed, labelled by the MLT name.

From `RAPID-CITY::rcMltTable`, one row per `rcMltName`.

- `Type`: Configured MLT type (1 normalMLT, 2 istMLT, 3 splitMLT, 4 virtualIstMLT) (`rcMltMltType`).
- `RunningType`: The type the MLT runs as, the same values as Type (`rcMltRunningType`).
- `SmltId`: Split MLT ID (`rcMltSmltId`).
- `Enabled`: Whether the MLT is enabled (TruthValue: 1 true, 2 false) (`rcMltEnable`).
- `AggregationState`: Link aggregation state (1 enable, 2 disable) (`rcMltAggOperState`).
- `MinLinks`: Active links below which the whole MLT is declared down (`rcMltAggMinLink`).
- `StateChangeTime`: TimeTicks, saved raw: RAPID-CITY describes it as 'the time value since the interface entered its current operational state', which may be the time elapsed or the sysUpTime of the change (unverified) (`rcMltAggTimeOfLastOperChange`).

#### VOSS - Virtual IST (5 minutes)

The virtual inter-switch trunk (vIST) between a pair of switches: whether its session is up, the peer's address and its VLAN.

From `RAPID-CITY` scalars.

- `Status`: vIST session status (1 up, 2 down) (`rcVirtualIstSessionStatus`).
- `PeerIp`: The vIST peer's address (`rcVirtualIstPeerIp`).
- `VlanId`: The vIST VLAN; 0 when not set (`rcVirtualIstVlanId`).

#### VOSS - SPB Adjacencies (5 minutes)

Each IS-IS adjacency of the SPB fabric (Fabric Connect): its state and three-way state, the neighbour's system ID, hold time and when it last came up, labelled by the neighbour's host name from the switch's own adjacency table.

From `ISIS-MIB::isisISAdjTable`, one row per `rcIsisAdjHostName`.

- `State`: Adjacency state (1 down, 2 initializing, 3 up, 4 failed) (`isisISAdjState`).
- `ThreeWayState`: Three-way handshake state (0 up, 1 initializing, 2 down, 3 failed) (`isisISAdj3WayState`).
- `NeighbourSystemId`: The neighbour's IS-IS system ID (`isisISAdjNeighSysID`).
- `HoldSecs`: The adjacency's holding time, seconds (`isisISAdjHoldTimer`).
- `LastUp`: sysUpTime when the adjacency last came up, hundredths of a second (TimeStamp, saved raw) (`isisISAdjLastUpTime`).

#### VOSS - RSMLT Peers (5 minutes)

Each routed split MLT (RSMLT) peer: its operational and administrative state, address, VLAN and VRF, and the hold-down and hold-up time remaining. The MIB gives no unit for the times.

From `RAPID-CITY::rcIpRsmltPeerTable`.

- `OperStatus`: RSMLT state (1 up, 2 down) (`rcIpRsmltPeerOperStatus`).
- `AdminEnabled`: Whether RSMLT is enabled (TruthValue: 1 true, 2 false) (`rcIpRsmltPeerAdminStatus`).
- `PeerIp`: The peer's address (`rcIpRsmltPeerIpAddr`).
- `VlanId`: The VLAN (`rcIpRsmltPeerVlanId`).
- `Vrf`: The VRF the peer belongs to (`rcIpRsmltPeerVrfName`).
- `HoldDownRemaining`: Hold-down time remaining, as the switch reports it (`rcIpRsmltPeerHoldDownTimeRemaining`).
- `HoldUpRemaining`: Hold-up time remaining, as the switch reports it (`rcIpRsmltPeerHoldUpTimeRemaining`).

#### VOSS - VLACP (5 minutes)

Virtual LACP on each port: whether it is enabled, its operational and port state, its timeout, and VLACP flaps as a rate and as a count since the previous poll, labelled by ifName.

From `RAPID-CITY::rcPortVLacpTable`, one row per `ifName`, also saved as `InterfaceName` for trigger messages.

- `AdminEnabled`: Whether VLACP is enabled on the port (TruthValue: 1 true, 2 false) (`rcPortVLacpAdminEnable`).
- `OperEnabled`: Whether VLACP is operationally enabled (TruthValue: 1 true, 2 false) (`rcPortVLacpOperEnable`).
- `PortState`: VLACP port state (1 up, 2 down) (`rcPortVLacpPortState`).
- `Timeout`: Timeout (1 short, 2 long) (`rcPortVLacpTimeout`).
- `Flaps`: VLACP flaps detected since the previous poll (`delta(rcPortVLacpTotalFlapCount)`).
- `FlapsPerSec`: VLACP flaps detected, per second (`rate(rcPortVLacpTotalFlapCount)`).

### Extreme VOSS Switch - Health and Performance Collection

#### VOSS - CPU and Memory (5 minutes)

CPU and memory use per slot on VOSS (VSP) switches.

From `RAPID-CITY::rcKhiSlotPerfTable`.

- `CpuPct`: CPU utilization now, percent (`rcKhiSlotCpuCurrentUtil`).
- `Cpu5MinPct`: CPU utilization averaged over the past 5 minutes, percent (`rcKhiSlotCpu5MinAve`).
- `MemoryPct`: Memory utilization now, percent (`rcKhiSlotMemUtil`).
- `MemoryFreeKB`: DRAM available, KB (`rcKhiSlotMemFree`).

#### VOSS - Fans (5 minutes)

Status of each fan and the temperature of the air entering it.

From `RAPID-CITY::rcChasFanTable`.

- `Status`: Fan status (unknown, up, down, notpresent) (`rcChasFanOperStatus`).
- `AmbientTemperatureC`: Temperature of the air entering the fan, degrees Celsius (`rcChasFanAmbientTemperature`).

#### VOSS - Power Supplies (5 minutes)

Status of each power supply.

From `RAPID-CITY::rcChasPowerSupplyTable`.

- `Status`: Power supply status (unknown, empty, up, down) (`rcChasPowerSupplyOperStatus`).

`delta(x)` is the change in counter `x` between the last two polls; `rate(x)` is that change divided by the monitor's polling period in seconds (TES's `$pollingSecs`), a per-second rate.

## Expected variations

A table monitor returns no rows when the device lacks the feature or hardware it covers. An empty table does not necessarily indicate a collection failure: first confirm that the feature is present and configured on the device.

- The MLT, virtual IST, RSMLT, VLACP and SPB monitors return no rows on a switch where the feature is not configured.
- VOSS - SPB Adjacencies takes its state from the standard ISIS-MIB adjacency table and its label, the neighbour's host name, from the switch's own adjacency table; whether the two share a row index is unverified, so a row's label may be missing.
- Rows of the SPB adjacency and RSMLT tables are identified by index values (circuit and adjacency numbers, ifIndex), which ThirdEye can show as OID digits.
- On a virtual VSP, VOSS - Fans returns no data, as a virtual device has no fans to report; the other monitors are unaffected.

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
