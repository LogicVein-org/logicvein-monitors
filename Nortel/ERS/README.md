# Nortel ERS and BayStack Switch Operational and Health and Performance Collections

Last updated: 2026-10-30

Tested against: Not yet validated against a device.

These monitor sets are for: Nortel and Extreme Ethernet Routing Switch and BayStack switches.

The folder holds two sets; assign both to the same devices:

- **Nortel ERS and BayStack Switch - Operational Collection** (6 monitors) covers what the device does (its role's state, load and services).
- **Nortel ERS and BayStack Switch - Health and Performance Collection** (1 monitor) covers the device itself (CPU, memory, storage and hardware).

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

- `BAY-STACK-PETH-EXT-MIB`
- `BAY-STACK-VRRP-EXT-MIB`
- `NORTEL-NETWORKS-MULTIPLE-SPANNING-TREE-MIB`
- `S5-CHASSIS-MIB`

ThirdEye's built-in MIB store already includes all of these modules, so no MIB import is needed. The MIB files are not included with these collections; to install or update a module, obtain the files as described below and import them with their dependencies.

### Obtaining the Nortel MIBs

Download the MIB files for the device's software release from Extreme Networks' support site (the Ethernet Routing Switch line is now Extreme Networks'), and import the modules listed above together with their dependencies.

## Installation

1. In **Inventory**, use **Tools > SNMP System Info** to verify that ThirdEye can query each target device.
2. Go to **Monitors > Sets** and import `Nortel ERS and BayStack Switch - Operational Collection.3em` and `Nortel ERS and BayStack Switch - Health and Performance Collection.3em`.
3. Assign **Nortel ERS and BayStack Switch - Operational Collection** and **Nortel ERS and BayStack Switch - Health and Performance Collection** to the appropriate devices.
4. Wait at least 5 minutes so every monitor has had a collection opportunity, then confirm that current data is present for the applicable monitors.

Automatic assignment to newly discovered devices is disabled in both sets. Review the sets before enabling automatic assignment in your environment.

These host queries match the devices the sets are meant for: `sysObjectID=1.3.6.1.4.1.45.*`.

## Included monitors

**Nortel ERS and BayStack Switch - Operational Collection** contains 6 SNMP monitors and **Nortel ERS and BayStack Switch - Health and Performance Collection** contains 1 SNMP monitor. Metrics carry friendly names; each lists the MIB object it reads or the formula it computes.

### Nortel ERS and BayStack Switch - Operational Collection

#### Nortel ERS - MSTP CIST (5 minutes)

The common and internal spanning tree (CIST) of multiple spanning tree (MSTP): the root bridge, the root cost and port, the time since the last topology change, and topology changes and root bridge changes, as rates and as counts since the previous poll.

From `NORTEL-NETWORKS-MULTIPLE-SPANNING-TREE-MIB` scalars.

- `Root`: The CIST root bridge (`nnMstCistRoot`).
- `RootCost`: Path cost to the CIST root (`nnMstCistRootCost`).
- `RootPort`: The port toward the CIST root (`nnMstCistRootPort`).
- `SinceTopologyChange`: Time since the last topology change, hundredths of a second (TimeTicks, saved raw) (`nnMstCistTimeSinceTopologyChange`).
- `TopologyChanges`: Topology changes since the previous poll (`delta(nnMstCistTopChanges)`).
- `TopologyChangesPerSec`: Topology changes, per second (`rate(nnMstCistTopChanges)`).
- `RootChanges`: Root bridge changes since the previous poll (`delta(nnMstCistNewRootBridgeCount)`).
- `RootChangesPerSec`: Root bridge changes, per second (`rate(nnMstCistNewRootBridgeCount)`).

#### Nortel ERS - MSTP Instances (5 minutes)

Each multiple spanning tree (MSTP) instance: whether it is enabled, its regional root, this bridge's priority, the root cost and port, the time since the last topology change, and topology changes and root changes, as rates and as counts since the previous poll. Rows are identified by instance.

From `NORTEL-NETWORKS-MULTIPLE-SPANNING-TREE-MIB::nnMstBridgeTable`.

- `Enabled`: Whether the instance is enabled (1 true, 2 false) (`nnMstBridgeEnabled`).
- `RegionalRoot`: The instance's regional root bridge (`nnMstBridgeRegionalRoot`).
- `Priority`: This bridge's priority in the instance (`nnMstBridgePriority`).
- `RootCost`: Path cost to the regional root (`nnMstBridgeRootCost`).
- `RootPort`: The port toward the regional root (`nnMstBridgeRootPort`).
- `SinceTopologyChange`: Time since the last topology change, hundredths of a second (TimeTicks, saved raw) (`nnMstBridgeTimeSinceTopologyChange`).
- `TopologyChanges`: Topology changes since the previous poll (`delta(nnMstBridgeTopChanges)`).
- `TopologyChangesPerSec`: Topology changes, per second (`rate(nnMstBridgeTopChanges)`).
- `RootChanges`: Regional root changes since the previous poll (`delta(nnMstBridgeNewRootCount)`).
- `RootChangesPerSec`: Regional root changes, per second (`rate(nnMstBridgeNewRootCount)`).

#### Nortel ERS - MSTP CIST Ports (5 minutes)

Each port's common and internal spanning tree (CIST) state and role, whether it is an edge port and point-to-point, with transitions to forwarding, topology change notification BPDUs received and invalid MST BPDUs received, as rates and as counts since the previous poll. Rows are identified by bridge port number.

From `NORTEL-NETWORKS-MULTIPLE-SPANNING-TREE-MIB::nnMstCistPortTable`.

- `State`: Port state (1 disabled, 2 discarding, 4 learning, 5 forwarding) (`nnMstCistPortState`).
- `Role`: Current port role (0 disabled, 1 alternate, 2 backup, 3 root, 4 designated) (`nnMstCistCurrentPortRole`).
- `Edge`: Whether it operates as an edge port (1 true, 2 false) (`nnMstCistPortOperEdgeStatus`).
- `PointToPoint`: Whether its link is point-to-point (1 true, 2 false) (`nnMstCistPortOperP2P`).
- `ToForwarding`: Transitions to forwarding since the previous poll (`delta(nnMstCistPortForwardTransitions)`).
- `ToForwardingPerSec`: Transitions to forwarding, per second (`rate(nnMstCistPortForwardTransitions)`).
- `TcnReceived`: Topology change notification BPDUs received since the previous poll (`delta(nnMstCistPortRxTcnBpduCount)`).
- `TcnReceivedPerSec`: Topology change notification BPDUs received, per second (`rate(nnMstCistPortRxTcnBpduCount)`).
- `InvalidMstReceived`: Invalid MST BPDUs received since the previous poll (`delta(nnMstCistPortInvalidMstBpduRxCount)`).
- `InvalidMstReceivedPerSec`: Invalid MST BPDUs received, per second (`rate(nnMstCistPortInvalidMstBpduRxCount)`).

#### Nortel ERS - MSTP Ports (5 minutes)

Each port's state and role in each multiple spanning tree (MSTP) instance, with transitions to forwarding and invalid BPDUs received, as rates and as counts since the previous poll. Rows are identified by bridge port number and instance.

From `NORTEL-NETWORKS-MULTIPLE-SPANNING-TREE-MIB::nnMstPortTable`.

- `State`: Port state (1 disabled, 2 discarding, 4 learning, 5 forwarding) (`nnMstPortState`).
- `Role`: Current port role (0 disabled, 1 alternate, 2 backup, 3 root, 4 designated, 5 master) (`nnMstPortCurrentPortRole`).
- `ToForwarding`: Transitions to forwarding since the previous poll (`delta(nnMstPortForwardTransitions)`).
- `ToForwardingPerSec`: Transitions to forwarding, per second (`rate(nnMstPortForwardTransitions)`).
- `InvalidReceived`: Invalid BPDUs received since the previous poll (`delta(nnMstPortInvalidBPDUsRcvd)`).
- `InvalidReceivedPerSec`: Invalid BPDUs received, per second (`rate(nnMstPortInvalidBPDUsRcvd)`).

#### Nortel ERS - PoE Ports (5 minutes)

Power over Ethernet (PoE) on each port as ERS reports it beyond the standard tables: its detailed status, whether an undercurrent or overcurrent was detected, and the voltage, current and power measured. Rows are identified by PSE group and port.

From `BAY-STACK-PETH-EXT-MIB::bspePethPsePortExtTable`.

- `DetailedStatus`: Detailed port status (1 poweredResistiveDiscovery, 2 poweredCapacitiveDiscovery, 3 poweredCiscoLegacyDiscovery, 4 invalidPD, 5 overloadFault, 6 underloadFault, 7 uvovFault, 8 powerManaged, 9 limitOverloadFault, 10 discoveryDisabled, 11 unableToResetTps, 12 unableToInitializeTps, 13 uninitialized, 14 nonexistent, 15 otherFault, 16 detectionStatus) (`bspePethPsePortExtDetailedStatus`).
- `CurrentStatus`: Current condition since last cleared (1 ok, 2 underCurrent, 3 overCurrent, 4 both) (`bspePethPsePortExtCurrentStatus`).
- `VoltageV`: Measured voltage, volts (from decivolts), 0 when not supplying power (`$bspePethPsePortExtMeasuredVoltage / 10.0`).
- `CurrentMa`: Measured current, milliamperes (`bspePethPsePortExtMeasuredCurrent`).
- `PowerMw`: Measured power, milliwatts (`bspePethPsePortExtMeasuredPower`).

#### Nortel ERS - VRRP Hold-Down and Backup Master (5 minutes)

For each VRRP virtual router, as ERS extends the standard table: whether its hold-down timer is active and the seconds left on it, and whether backup master is enabled and up. Rows are identified by ifIndex and virtual router ID.

From `BAY-STACK-VRRP-EXT-MIB::bsveVrrpOperExtTable`.

- `HoldDownState`: Hold-down timer state (1 dormant, 2 active) (`bsveVrrpOperExtHoldDownState`).
- `HoldDownSecsLeft`: Seconds left on the hold-down timer (`bsveVrrpOperExtHoldDownTimeRemaining`).
- `BackupMasterEnabled`: Whether backup master is enabled (1 true, 2 false) (`bsveVrrpOperExtBackUpMasterEnabled`).
- `BackupMasterState`: Backup master state (1 up, 2 down) (`bsveVrrpOperExtBackUpMasterState`).

### Nortel ERS and BayStack Switch - Health and Performance Collection

#### Nortel ERS - CPU and Memory (5 minutes)

CPU busy over 1 and 10 minutes and memory per stack unit (S5-CHASSIS-MIB).

From `S5-CHASSIS-MIB::s5ChasUtilTable`.

- `Cpu1MinPct`: CPU busy over the last minute, percent (`s5ChasUtilCPUUsageLast1Minute`).
- `Cpu10MinPct`: CPU busy over the last 10 minutes, percent (`s5ChasUtilCPUUsageLast10Minutes`).
- `MemoryTotalMB`: Total RAM, MB (`s5ChasUtilMemoryTotalMB`).
- `MemoryAvailableMB`: Available RAM, MB (`s5ChasUtilMemoryAvailableMB`).
- `MemoryUsedPct`: RAM in use as a percentage of total (`(($MemoryTotalMB - $MemoryAvailableMB) / $MemoryTotalMB) * 100.0`).

`delta(x)` is the change in counter `x` between the last two polls; `rate(x)` is that change divided by the monitor's polling period in seconds (TES's `$pollingSecs`), a per-second rate.

## Expected variations

A table monitor returns no rows when the device lacks the feature or hardware it covers. An empty table does not necessarily indicate a collection failure: first confirm that the feature is present and configured on the device.

- The MSTP monitors return values only where the switch runs multiple spanning tree; the PoE and VRRP monitors can return no rows on a switch without PoE hardware or VRRP.
- Rows of the MSTP port, PoE and VRRP tables are identified by index values (bridge port number and instance, PSE group and port, ifIndex and router ID), which ThirdEye can show as OID digits.

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
