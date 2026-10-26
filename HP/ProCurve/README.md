# HP ProCurve Switch Operational and Health and Performance Collections

Last updated: 2026-10-24

Tested against: **HP ProCurve Switch - Operational Collection**: not yet validated against a device; **HP ProCurve Switch - Health and Performance Collection**: HP ProCurve 2626

These monitor sets are for: HP ProCurve switches, and the same family under its later name ArubaOS-Switch (2530, 2930, 3810, 5400R and older ProCurve models).

The folder holds two sets; assign both to the same devices:

- **HP ProCurve Switch - Operational Collection** (7 monitors) covers what the device does (its role's state, load and services).
- **HP ProCurve Switch - Health and Performance Collection** (4 monitors) covers the device itself (CPU, memory, storage and hardware).

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

- `DT-MIB`
- `HP-ICF-BRIDGE`
- `HP-ICF-CHASSIS`
- `HP-ICF-POE-MIB`
- `HP-ICF-VRRP-MIB`
- `HP-STACK-MIB`
- `NETSWITCH-MIB`
- `STATISTICS-MIB`

ThirdEye's built-in MIB store already includes all of these modules, so no MIB import is needed. The MIB files are not included with these collections; to install or update a module, obtain the files as described below and import them with their dependencies.

### Obtaining the HP ProCurve MIBs

Download the MIB files for the device's software release from the HPE Aruba Networking Support Portal (ProCurve switches are now HPE Aruba Networking products), and import the modules listed above together with their dependencies.

## Installation

1. In **Inventory**, use **Tools > SNMP System Info** to verify that ThirdEye can query each target device.
2. Go to **Monitors > Sets** and import `HP ProCurve Switch - Operational Collection.3em` and `HP ProCurve Switch - Health and Performance Collection.3em`.
3. Assign **HP ProCurve Switch - Operational Collection** and **HP ProCurve Switch - Health and Performance Collection** to the appropriate devices.
4. Wait at least 1 minute so every monitor has had a collection opportunity, then confirm that current data is present for the applicable monitors.

Automatic assignment to newly discovered devices is disabled in both sets. Review the sets before enabling automatic assignment in your environment.

These host queries match the devices the sets are meant for: `sysObjectID=1.3.6.1.4.1.11.2.3.7.11.*`.

## Included monitors

**HP ProCurve Switch - Operational Collection** contains 7 SNMP monitors and **HP ProCurve Switch - Health and Performance Collection** contains 4 SNMP monitors. Metrics carry friendly names; each lists the MIB object it reads or the formula it computes.

### HP ProCurve Switch - Operational Collection

#### HP ProCurve - PoE Ports (1 minute)

Power over Ethernet (PoE) on each port as ArubaOS-Switch reports it beyond the standard tables: its operational status, power supplied, power the powered device draws, average and peak power, current and voltage. Rows are identified by PSE group and port.

From `HP-ICF-POE-MIB::hpicfPoePethPsePortTable`.

- `OperStatus`: Port PoE status (1 deny, 2 off, 3 on) (`hpicfPoePethPsePortOperStatus`).
- `SuppliedMw`: Power supplied at the port, milliwatts (`hpicfPoePethPsePortPower`).
- `DrawnMw`: Power the powered device actually draws, milliwatts (`hpicfPoePethPsePortActualPower`).
- `AverageW`: Average power used by the powered device, watts (the MIB's UNITS) (`hpicfPoePethPsePortAveragePower`).
- `PeakW`: Peak power used by the powered device, watts (the MIB's UNITS) (`hpicfPoePethPsePortPeakPower`).
- `CurrentMa`: Current supplied, milliamperes (`hpicfPoePethPsePortCurrent`).
- `VoltageV`: Voltage supplied, volts, from the MIB's dV (its description says tens of volts) (`$hpicfPoePethPsePortVoltage / 10.0`).

#### HP ProCurve - PoE Power (1 minute)

Power over Ethernet (PoE) power on each power source equipment (PSE): total power available from internal and external supplies, power allocated to its ports, failover power and redundant power. Rows are identified by entPhysicalIndex.

From `HP-ICF-POE-MIB::hpicfPseFeaturesTable`.

- `AvailableW`: Total power available, internal and external supplies, watts (`hpicfPseAvailablePower`).
- `AllocatedW`: Total power allocated to the PSE's ports, watts (`hpicfPseUsedPower`).
- `FailoverW`: Failover power, watts (`hpicfPseFailoverPower`).
- `RedundantW`: Redundant PoE power available, watts (`hpicfPseRedundantPower`).

#### HP ProCurve - PoE PSE Load (1 minute)

Power over Ethernet (PoE) load on each PSE group: its PoE operating state, peak and average power, and remaining guaranteed power, from two tables that extend the standard main PSE table, matched by row index. Rows are identified by PSE group.

From `HP-ICF-POE-MIB::hpicfPoePethPsePowerTable`.

- `State`: PoE operating state (1 poeOn, 2 poePlusOn, 3 off, 4 fault) (`hpicfPoePethPseOperState`).
- `PeakW`: Peak power of the PSE, watts (`hpicfPoePethPsePeakPower`).
- `AverageW`: Average power of the PSE, watts (`hpicfPoePethPseAvgPower`).
- `GuaranteedRemainingW`: Guaranteed power remaining, watts (`hpicfPoePethPseRemGrntPower`).

#### HP ProCurve - Distributed Trunking (1 minute)

Distributed trunking (DT) between a pair of switches: inter-switch connection (ISC) hello packets and peer keepalive packets sent and received, as rates and as counts since the previous poll. Received keepalives falling to zero mean the peer is not heard.

From `DT-MIB` scalars.

- `HellosSent`: ISC hello packets sent to the peer since the previous poll (`delta(hpSwitchDtIscHelloPktsSent)`).
- `HellosSentPerSec`: ISC hello packets sent to the peer, per second (`rate(hpSwitchDtIscHelloPktsSent)`).
- `HellosReceived`: ISC hello packets received from the peer since the previous poll (`delta(hpSwitchDtIscHelloPktsRecv)`).
- `HellosReceivedPerSec`: ISC hello packets received from the peer, per second (`rate(hpSwitchDtIscHelloPktsRecv)`).
- `KeepAlivesSent`: Peer keepalive packets sent since the previous poll (`delta(hpSwitchDtPeerKeepAlivePktsSent)`).
- `KeepAlivesSentPerSec`: Peer keepalive packets sent, per second (`rate(hpSwitchDtPeerKeepAlivePktsSent)`).
- `KeepAlivesReceived`: Valid peer keepalive packets received since the previous poll (`delta(hpSwitchDtPeerKeepAlivePktsRecv)`).
- `KeepAlivesReceivedPerSec`: Valid peer keepalive packets received, per second (`rate(hpSwitchDtPeerKeepAlivePktsRecv)`).

#### HP ProCurve - Distributed Trunking MAC Sync (1 minute)

Distributed trunking (DT) MAC address synchronisation over the inter-switch connection: MAC learn and MAC age-out packets sent to and received from the peer, as rates and as counts since the previous poll.

From `DT-MIB` scalars.

- `LearnsSent`: MAC learn packets sent to the peer since the previous poll (`delta(hpSwitchDtIscMACLearnPktsSent)`).
- `LearnsSentPerSec`: MAC learn packets sent to the peer, per second (`rate(hpSwitchDtIscMACLearnPktsSent)`).
- `LearnsReceived`: MAC learn packets received from the peer since the previous poll (`delta(hpSwitchDtIscMACLearnPktsRecv)`).
- `LearnsReceivedPerSec`: MAC learn packets received from the peer, per second (`rate(hpSwitchDtIscMACLearnPktsRecv)`).
- `AgeOutsSent`: MAC age-out packets sent to the peer since the previous poll (`delta(hpSwitchDtIscMACAgedPktsSent)`).
- `AgeOutsSentPerSec`: MAC age-out packets sent to the peer, per second (`rate(hpSwitchDtIscMACAgedPktsSent)`).
- `AgeOutsReceived`: MAC age-outs received from the peer since the previous poll (`delta(hpSwitchDtIscMACAgedPktsRecv)`).
- `AgeOutsReceivedPerSec`: MAC age-outs received from the peer, per second (`rate(hpSwitchDtIscMACAgedPktsRecv)`).

#### HP ProCurve - VRRP Near Failovers (1 minute)

Near failovers of each backup VRRP virtual router: times it went two advertisement intervals without hearing the master, as a rate and as a count since the previous poll. Rows are identified by ifIndex and virtual router ID.

From `HP-ICF-VRRP-MIB::hpicfVrrpStatsTable`.

- `NearFailovers`: Near failovers since the previous poll (`delta(hpicfVrrpStatsNearFailovers)`).
- `NearFailoversPerSec`: Near failovers, per second (`rate(hpicfVrrpStatsNearFailovers)`).

#### HP ProCurve - Loop Protection (1 minute)

Loop protection on each port: whether it is enabled, whether a loop is detected, loops detected as a rate and as a count since the previous poll, when the last loop was detected, and the VLAN it was detected on. Rows are identified by ifIndex.

From `HP-ICF-BRIDGE::hpicfBridgeLoopProtectPortTable`.

- `Enabled`: Whether loop protection is enabled (1 true, 2 false) (`hpicfBridgeLoopProtectPortEnable`).
- `LoopDetected`: Whether a loop is detected (1 true, 2 false) (`hpicfBridgeLoopProtectPortLoopDetected`).
- `Loops`: Loops detected since the previous poll (`delta(hpicfBridgeLoopProtectPortLoopCount)`).
- `LoopsPerSec`: Loops detected, per second (`rate(hpicfBridgeLoopProtectPortLoopCount)`).
- `LastLoopTime`: sysUpTime when a loop was last detected, 0 for never (TimeStamp, saved raw) (`hpicfBridgeLoopProtectPortLastLoopTime`).
- `LoopVlan`: The VLAN the loop was detected on (`hpicfBridgeLoopDetectedVlan`).

### HP ProCurve Switch - Health and Performance Collection

#### HP ProCurve - CPU Utilization (1 minute)

CPU busy percentage of the switch.

From `STATISTICS-MIB` scalars.

- `CpuPct`: CPU utilization, percent (`hpSwitchCpuStat`).

#### HP ProCurve - Memory (1 minute)

Installed, free and allocated memory per slot (stack member or module).

From `NETSWITCH-MIB::hpLocalMemTable`.

- `TotalBytes`: Installed memory, bytes (`hpLocalMemTotalBytes`).
- `FreeBytes`: Unallocated memory, bytes (`hpLocalMemFreeBytes`).
- `UsedPct`: Allocated memory as a percentage of installed memory (`$hpLocalMemAllocBytes * 100 / $TotalBytes`).

#### HP ProCurve - Chassis Sensors (1 minute)

State of each fan, power supply and temperature sensor, and how often each has failed.

From `HP-ICF-CHASSIS::hpicfSensorTable`, one row per `hpicfSensorDescr`.

- `Status`: Sensor status (unknown, bad, warning, good, notPresent) (`hpicfSensorStatus`).
- `FailuresPerSec`: Transitions of the sensor into the bad state, per second (`rate(hpicfSensorFailures)`).
- `Failures`: Transitions of the sensor into the bad state since the previous poll (`delta(hpicfSensorFailures)`).

#### HP ProCurve - Stack Ports (1 minute)

State of each stacking port, per stack member, on stacked HP ProCurve switches.

From `HP-STACK-MIB::hpStackPortTable`.

- `OperStatus`: Operational status (1 up, 2 down, 3 disabled, 4 blocked) (`hpStackPortOperStatus`).
- `AdminStatus`: Administrative status (1 enable, 2 disable) (`hpStackPortAdminStatus`).
- `PortType`: Stacking port type (1 back plane, 2 front plane) (`hpStackPortType`).

`delta(x)` is the change in counter `x` between the last two polls; `rate(x)` is that change divided by the monitor's polling period in seconds (TES's `$pollingSecs`), a per-second rate.

## Expected variations

A table monitor returns no rows when the device lacks the feature or hardware it covers. An empty table does not necessarily indicate a collection failure: first confirm that the feature is present and configured on the device.

- The PoE, distributed trunking, VRRP and loop protection monitors can return no rows or values on a switch without PoE hardware or where the feature is not configured.
- Rows of the PoE, VRRP and loop protection tables are identified by index values (PSE group and port, entPhysicalIndex, ifIndex and virtual router ID), which ThirdEye can show as OID digits.

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
