# Huawei VRP Device Operational and Health and Performance Collections

Last updated: 2026-10-30

Tested against: Not yet validated against a device.

These monitor sets are for: Huawei routers and switches running VRP.

The folder holds two sets; assign both to the same devices:

- **Huawei VRP Device - Operational Collection** (6 monitors) covers what the device does (its role's state, load and services).
- **Huawei VRP Device - Health and Performance Collection** (3 monitors) covers the device itself (CPU, memory, storage and hardware).

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
- `HUAWEI-ENTITY-EXTENT-MIB`
- `HUAWEI-IF-EXT-MIB`
- `HUAWEI-L2VLAN-MIB`
- `HUAWEI-MLSR-DEV-ADM-MIB`
- `HUAWEI-MSTP-MIB`
- `HUAWEI-RRPP-MIB`

ThirdEye's built-in MIB store already includes all of these modules, so no MIB import is needed. The MIB files are not included with these collections; to install or update a module, obtain the files as described below and import them with their dependencies.

### Obtaining the Huawei MIBs

Download the MIB files for the device's software release from Huawei's support site, and import the modules listed above together with their dependencies.

## Installation

1. In **Inventory**, use **Tools > SNMP System Info** to verify that ThirdEye can query each target device.
2. Go to **Monitors > Sets** and import `Huawei VRP Device - Operational Collection.3em` and `Huawei VRP Device - Health and Performance Collection.3em`.
3. Assign **Huawei VRP Device - Operational Collection** and **Huawei VRP Device - Health and Performance Collection** to the appropriate devices.
4. Wait at least 5 minutes so every monitor has had a collection opportunity, then confirm that current data is present for the applicable monitors.

Automatic assignment to newly discovered devices is disabled in both sets. Review the sets before enabling automatic assignment in your environment.

These host queries match the devices the sets are meant for: `sysObjectID=1.3.6.1.4.1.2011.*`.

## Included monitors

**Huawei VRP Device - Operational Collection** contains 6 SNMP monitors and **Huawei VRP Device - Health and Performance Collection** contains 3 SNMP monitors. Metrics carry friendly names; each lists the MIB object it reads or the formula it computes.

### Huawei VRP Device - Operational Collection

#### Huawei - Eth-Trunks (5 minutes)

Each Eth-Trunk (link aggregation): its type, working mode and state, minimum links and bandwidth, and its ifIndex. Rows are identified by trunk number.

From `HUAWEI-IF-EXT-MIB::hwTrunkIfTable`.

- `Type`: Trunk type (1 ethTrunk, 2 ipTrunk) (`hwTrunkIfType`).
- `WorkingMode`: Working mode (-1 invalid, 1 masterBackup, 2 normalMode, 3 lacpMode) (`hwTrunkIfWorkingMode`).
- `WorkingState`: Working state of a master/backup trunk (-1 invalid, 1 generalMode, 2 initialization, 3 masterWorking, 4 backupWorking) (`hwTrunkIfWorkingState`).
- `MinLinks`: Member links that must be up for the trunk to come up (`hwTrunkIfMinLinkNum`).
- `BandwidthMbps`: Current bandwidth, megabits per second; -1 for a trunk other than an Eth-Trunk (`hwTrunkBandwidth`).
- `IfIndex`: The trunk interface's ifIndex (`hwTrunkIfIndex`).

#### Huawei - Eth-Trunk Members (5 minutes)

Each Eth-Trunk member port's state, whether LACP selected it, whether LACP is enabled and in which mode, whether it is the master of a master/backup trunk, and its weight and priority. Rows are identified by trunk number and the member's ifIndex.

From `HUAWEI-IF-EXT-MIB::hwTrunkMemTable`.

- `OperStatus`: Member port state (1 up, 2 down) (`hwTrunkOperstatus`).
- `Selected`: Whether LACP selected the port (-1 invalid, 1 trunkSelected, 2 trunkDeselected) (`hwTrunkSelectStatus`).
- `LacpEnabled`: Whether LACP is enabled (1 enabled, 2 disabled) (`hwTrunkLacpStatus`).
- `LacpMode`: LACP mode (1 invalid, 2 active, 3 passive) (`hwTrunkPortLacpMode`).
- `Master`: Master or slave in a master/backup trunk (-1 invalid, 1 portSlave, 2 portMaster) (`hwTrunkPortMaster`).
- `Weight`: The port's share of the trunk's load (`hwTrunkPortWeight`).
- `Priority`: The port's LACP priority (`hwTrunkPortPriority`).

#### Huawei - MSTP Instances (5 minutes)

Each multiple spanning tree instance: this bridge's priority, the root bridge, root port and path cost, the configured root type and remaining hops. Rows are identified by instance ID.

From `HUAWEI-MSTP-MIB::hwMstpInstanceTable`.

- `BridgePriority`: This bridge's priority in the instance (`hwMstpiBridgePriority`).
- `DesignatedRoot`: Bridge ID of the instance's root (`hwMstpiDesignedRoot`).
- `RootPort`: The root port (`hwMstpiRootPort`).
- `RootCost`: Path cost to the root (`hwMstpiRootPathCost`).
- `RootType`: Configured root role (0 normal, 1 secondary, 2 primary) (`hwMstpiRootType`).
- `RemainingHops`: Remaining hops (`hwMstpiRemainingHops`).

#### Huawei - RRPP Rings (5 minutes)

Each Rapid Ring Protection Protocol (RRPP) ring: whether it is enabled and active, its state, this switch's node mode, the ring level and its primary and secondary ports. Rows are identified by domain and ring.

From `HUAWEI-RRPP-MIB::hwRrppRingTable`.

- `Enabled`: Whether RRPP is enabled on the ring (1 enabled, 2 disabled) (`hwRrppRingEnableStatus`).
- `Active`: Whether the ring is active (1 active, 2 inactive) (`hwRrppRingActive`).
- `State`: Ring state (1 unknown, 2 health, 3 fault, 4 complete, 5 failed, 6 linkup, 7 linkdown, 8 preforwarding, 9 linkupnotify, 10 linkdownnotify, 11 preforwardnotify) (`hwRrppRingState`).
- `NodeMode`: This switch's node mode (1 master, 2 transit, 3 edge, 4 assistantEdge) (`hwRrppRingNodeMode`).
- `Level`: Ring level (0 majorRing, 1 subRing) (`hwRrppRingLevel`).
- `PrimaryPort`: ifIndex of the primary (or common) port; 0 when unset (`hwRrppRingPrimaryPort`).
- `SecondaryPort`: ifIndex of the secondary (or edge) port; 0 when unset (`hwRrppRingSecondaryPort`).

#### Huawei - RRPP Ports (5 minutes)

Each RRPP ring port's role and state, and illegal RRPP packets received and packets that failed to send, as rates and as counts since the previous poll.

From `HUAWEI-RRPP-MIB::hwRrppPortTable`.

- `Role`: The port's RRPP role (1 primary, 2 secondary, 3 common, 4 edge) (`hwRrppPortRole`).
- `State`: Port state (1 unknown, 2 unblocked, 3 blocked, 4 down) (`hwRrppPortState`).
- `RxErrors`: Illegal RRPP packets received since the previous poll (`delta(hwRrppPortRXError)`).
- `RxErrorsPerSec`: Illegal RRPP packets received, per second (`rate(hwRrppPortRXError)`).
- `TxErrors`: RRPP packets that failed to send since the previous poll (`delta(hwRrppPortTXError)`).
- `TxErrorsPerSec`: RRPP packets that failed to send, per second (`rate(hwRrppPortTXError)`).

#### Huawei - VLANs (5 minutes)

Each VLAN's type, administrative state, how it was created and its property, and whether it has a VLAN interface, labelled by the VLAN name.

From `HUAWEI-L2VLAN-MIB::hwL2VlanMIBTable`, one row per `hwL2VlanName`.

- `Type`: VLAN type (1 superVlan, 2 commonVlan, 3 subVlan, 4 muxVlan, 5 muxSubVlan, 6 protocolTransVlan) (`hwL2VlanType`).
- `AdminStatus`: Administrative state (1 enabled, 2 disabled) (`hwL2VlanAdminStatus`).
- `CreatedBy`: How the VLAN was created (1 other, 2 static, 3 dynamic) (`hwL2VlanCreateStatus`).
- `Property`: VLAN property (1 default, 2 backboneVlan, 3 mutilcastVlan, 4 userVlan) (`hwL2VlanProperty`).
- `VlanIfIndex`: ifIndex of the VLAN interface; -1 when none (`hwL2VlanIfIndex`).

### Huawei VRP Device - Health and Performance Collection

#### Huawei - Board Health (5 minutes)

CPU and memory use, temperature and state per board or module (HUAWEI-ENTITY-EXTENT-MIB).

From `HUAWEI-ENTITY-EXTENT-MIB::hwEntityStateTable`, one row per `entPhysicalName`.

- `CpuPct`: CPU usage of the entity, percent (`hwEntityCpuUsage`).
- `MemoryPct`: Memory used, percent (`hwEntityMemUsage`).
- `Temperature`: Temperature as the entity reports it (the MIB does not state the unit) (`hwEntityTemperature`).
- `OperStatus`: Operational state (`hwEntityOperStatus`).

#### Huawei - System CPU and Memory (5 minutes)

System-wide CPU busy percentage and memory, from the older HUAWEI-MLSR-DEV-ADM-MIB objects many VRP releases still answer.

From `HUAWEI-MLSR-DEV-ADM-MIB` scalars.

- `Cpu5SecPct`: CPU busy over the last 5 seconds, percent (`hwCpuCostRatePer5Sec`).
- `Cpu5MinPct`: CPU busy over the last 5 minutes, percent (`hwCpuCostRatePer5Minute`).
- `UsedBytes`: Memory in use, bytes (`hwUsedMemory`).
- `FreeBytes`: Memory free, bytes (`hwFreeMemory`).
- `MemoryUsedPct`: Memory in use as a percentage of used plus free (`($UsedBytes / ($UsedBytes + $FreeBytes)) * 100.0`).

#### Huawei - System Environment (5 minutes)

Temperature, fan and power status from the older system-wide objects.

From `HUAWEI-MLSR-DEV-ADM-MIB` scalars.

- `TemperatureStatus`: Temperature status (ok, overcold, overhot) (`hwSysAirFlowTempIn`).
- `FanStatus`: Fan status (running, stopping, nofan) (`hwFansStatus`).
- `PowerStatus`: Power status (ok, fail, nopower) (`hwPowerStatus`).
- `SecondPowerStatus`: Second power supply status (ok, fail, nopower) (`hwSecondPowerStatus`).

`delta(x)` is the change in counter `x` between the last two polls; `rate(x)` is that change divided by the monitor's polling period in seconds (TES's `$pollingSecs`), a per-second rate.

## Expected variations

A table monitor returns no rows when the device lacks the feature or hardware it covers. An empty table does not necessarily indicate a collection failure: first confirm that the feature is present and configured on the device.

- The Eth-Trunk, MSTP, RRPP and VLAN monitors return no rows on a device where the feature is not configured.
- Rows of the Eth-Trunk, MSTP and RRPP tables are identified by index values (trunk numbers, member ifIndex, instance, domain and ring), which ThirdEye can show as OID digits.

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
