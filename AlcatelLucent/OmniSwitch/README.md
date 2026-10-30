# Alcatel-Lucent OmniSwitch Operational and Health and Performance Collections

Last updated: 2026-10-30

Tested against: Not yet validated against a device.

These monitor sets are for: Alcatel-Lucent Enterprise OmniSwitch switches.

The folder holds two sets; assign both to the same devices:

- **Alcatel-Lucent OmniSwitch - Operational Collection** (7 monitors) covers what the device does (its role's state, load and services).
- **Alcatel-Lucent OmniSwitch - Health and Performance Collection** (5 monitors) covers the device itself (CPU, memory, storage and hardware).

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

- `ALCATEL-IND1-CHASSIS-MIB`
- `ALCATEL-IND1-ERP-MIB`
- `ALCATEL-IND1-HEALTH-MIB`
- `ALCATEL-IND1-INLINE-POWER-MIB`
- `ALCATEL-IND1-LAG-MIB`
- `ALCATEL-IND1-STACK-MANAGER-MIB`
- `ALCATEL-IND1-VLAN-MGR-MIB`
- `ALCATEL-IND1-VRRP3-MIB`

ThirdEye's built-in MIB store already includes all of these modules, so no MIB import is needed. The MIB files are not included with these collections; to install or update a module, obtain the files as described below and import them with their dependencies.

### Obtaining the Alcatel-Lucent MIBs

Download the MIB files for the device's software release from Alcatel-Lucent Enterprise's support site, and import the modules listed above together with their dependencies.

## Installation

1. In **Inventory**, use **Tools > SNMP System Info** to verify that ThirdEye can query each target device.
2. Go to **Monitors > Sets** and import `Alcatel-Lucent OmniSwitch - Operational Collection.3em` and `Alcatel-Lucent OmniSwitch - Health and Performance Collection.3em`.
3. Assign **Alcatel-Lucent OmniSwitch - Operational Collection** and **Alcatel-Lucent OmniSwitch - Health and Performance Collection** to the appropriate devices.
4. Wait at least 5 minutes so every monitor has had a collection opportunity, then confirm that current data is present for the applicable monitors.

Automatic assignment to newly discovered devices is disabled in both sets. Review the sets before enabling automatic assignment in your environment.

These host queries match the devices the sets are meant for: `sysObjectID=1.3.6.1.4.1.6486.*`.

## Included monitors

**Alcatel-Lucent OmniSwitch - Operational Collection** contains 7 SNMP monitors and **Alcatel-Lucent OmniSwitch - Health and Performance Collection** contains 5 SNMP monitors. Metrics carry friendly names; each lists the MIB object it reads or the formula it computes.

### Alcatel-Lucent OmniSwitch - Operational Collection

#### OmniSwitch - ERP Rings (5 minutes)

Each Ethernet Ring Protection (ERP, G.8032) ring: whether it is enabled, its state, its service VLAN, and when its state last changed. Rows are identified by ring ID.

From `ALCATEL-IND1-ERP-MIB::alaErpRingTable`.

- `Status`: Admin status (1 enabled, 2 disabled) (`alaErpRingStatus`).
- `State`: Ring state (0 idle, 1 protection; the published MIB adds 2 init, 3 manualSwitch, 4 forcedSwitch and 5 pending, which TES's revision lacks) (`alaErpRingState`).
- `ServiceVlan`: The ring's service VLAN (`alaErpRingServiceVid`).
- `LastStateChange`: sysUpTime at the last state change (TimeStamp, saved raw) (`alaErpRingLastStateChange`).

#### OmniSwitch - ERP Ports (5 minutes)

Each Ethernet Ring Protection (ERP) ring port: whether it is forwarding or blocking and whether it is the ring protection link (RPL), with R-APS signal-fail packets sent, received and dropped, errored R-APS packets and RPL-block packets received, as rates and as counts since the previous poll, from the ring port and statistics tables matched by row index. Rows are identified by ring ID and ifIndex.

From `ALCATEL-IND1-ERP-MIB::alaErpRingPortTable`.

- `Status`: Port status (1 forwarding, 2 blocking) (`alaErpRingPortStatus`).
- `Type`: Port type (1 nonRpl, 2 rpl) (`alaErpRingPortType`).
- `SignalFailSent`: R-APS signal-fail packets sent since the previous poll (`delta(alaErpStatsSignalFailPduTx)`).
- `SignalFailSentPerSec`: R-APS signal-fail packets sent, per second (`rate(alaErpStatsSignalFailPduTx)`).
- `SignalFailReceived`: R-APS signal-fail packets received since the previous poll (`delta(alaErpStatsSignalFailPduRx)`).
- `SignalFailReceivedPerSec`: R-APS signal-fail packets received, per second (`rate(alaErpStatsSignalFailPduRx)`).
- `SignalFailDropped`: Valid R-APS signal-fail packets dropped since the previous poll (`delta(alaErpStatsSignalFailPduDrop)`).
- `SignalFailDroppedPerSec`: Valid R-APS signal-fail packets dropped, per second (`rate(alaErpStatsSignalFailPduDrop)`).
- `PduErrors`: Errored R-APS packets received since the previous poll (`delta(alaErpStatsPDUErr)`).
- `PduErrorsPerSec`: Errored R-APS packets received, per second (`rate(alaErpStatsPDUErr)`).
- `RplBlockReceived`: R-APS RPL-block packets received since the previous poll (`delta(alaErpStatsRPLBlockPDURx)`).
- `RplBlockReceivedPerSec`: R-APS RPL-block packets received, per second (`rate(alaErpStatsRPLBlockPDURx)`).

#### OmniSwitch - VLANs (5 minutes)

Each VLAN's admin and operational status. Rows are identified by VLAN ID, labelled by description.

From `ALCATEL-IND1-VLAN-MGR-MIB::vlanTable`, one row per `vlanDescription`.

- `AdminStatus`: Admin status (1 enabled, 2 disabled) (`vlanAdmStatus`).
- `OperStatus`: Operational status (1 active, 2 inactive) (`vlanOperStatus`).

#### OmniSwitch - VRRP Virtual Routers (5 minutes)

Each VRRP virtual router: its state, priority, master's address and uptime, with times it became master, advertisements received, priority-zero packets received and advertisement interval errors, as rates and as counts since the previous poll, from the operations and statistics tables matched by row index. Rows are identified by IP version, virtual router ID and ifIndex.

From `ALCATEL-IND1-VRRP3-MIB::alaVrrp3OperTable`.

- `State`: State (1 initialize, 2 backup, 3 master) (`alaVrrp3OperState`).
- `Priority`: Priority (`alaVrrp3OperPriority`).
- `MasterAddress`: The master's IP address (`alaVrrp3OperMasterIpAddr`).
- `UpTime`: sysUpTime when the virtual router left the initialize state (TimeStamp, saved raw) (`alaVrrp3OperUpTime`).
- `BecameMaster`: Times it became master since the previous poll (`delta(alaVrrp3StatsBecomeMaster)`).
- `BecameMasterPerSec`: Times it became master, per second (`rate(alaVrrp3StatsBecomeMaster)`).
- `AdvertisementsReceived`: Advertisements received since the previous poll (`delta(alaVrrp3StatsAdvertiseRcvd)`).
- `AdvertisementsReceivedPerSec`: Advertisements received, per second (`rate(alaVrrp3StatsAdvertiseRcvd)`).
- `PriorityZeroReceived`: Priority-zero packets received since the previous poll (`delta(alaVrrp3StatsPriZeroPktsRcvd)`).
- `PriorityZeroReceivedPerSec`: Priority-zero packets received, per second (`rate(alaVrrp3StatsPriZeroPktsRcvd)`).
- `IntervalErrors`: Advertisements received with the wrong interval since the previous poll (`delta(alaVrrp3StatsAdvIntervalErrors)`).
- `IntervalErrorsPerSec`: Advertisements received with the wrong interval, per second (`rate(alaVrrp3StatsAdvIntervalErrors)`).

#### OmniSwitch - LAGs (5 minutes)

Each link aggregation group (LAG): its size, whether it uses LACP, its admin and operational state, the ports selected and attached, and its primary port. Rows are identified by the LAG's ifIndex, labelled by name.

From `ALCATEL-IND1-LAG-MIB::alclnkaggAggTable`, one row per `alclnkaggAggName`.

- `Size`: Maximum number of ports (`alclnkaggAggSize`).
- `Lacp`: Whether LACP is used (0 lacpOff, 1 lacpOn) (`alclnkaggAggLacpType`).
- `AdminState`: Admin state (1 enable, 2 disable) (`alclnkaggAggAdminState`).
- `OperState`: Operational state (1 up, 2 down, 3 logicPortCreatFailed, 4 qReservationFailed) (`alclnkaggAggOperState`).
- `SelectedPorts`: Ports selected (`alclnkaggAggNbrSelectedPorts`).
- `AttachedPorts`: Ports attached (`alclnkaggAggNbrAttachedPorts`).
- `PrimaryPort`: The primary port's ifIndex (`alclnkaggAggPrimaryPortIndex`).

#### OmniSwitch - LAG Ports (5 minutes)

Each port configured in a LAG: its admin and operational state, aggregation state, link state, whether it is the primary port, and the LAG it is attached to. Rows are identified by ifIndex.

From `ALCATEL-IND1-LAG-MIB::alclnkaggAggPortTable`.

- `AdminState`: Admin state (1 enable, 2 disable) (`alclnkaggAggPortAdminState`).
- `OperState`: Operational state (1 up, 2 down, 3 notAttached, 4 notAggregable) (`alclnkaggAggPortOperState`).
- `State`: Aggregation state (1 created, 2 configurable, 3 configured, 4 selected, 5 reserved, 6 attached) (`alclnkaggAggPortState`).
- `LinkState`: Link state (1 up, 2 down) (`alclnkaggAggPortLinkState`).
- `Primary`: Whether it is the primary port (1 yes, 2 no, 3 notSignificant) (`alclnkaggAggPortPrimary`).
- `AttachedLag`: ifIndex of the LAG it is attached to (`alclnkaggAggPortAttachedAggID`).

#### OmniSwitch - PoE Ports (5 minutes)

Power over Ethernet (PoE) on each port as OmniSwitch reports it beyond the standard tables: whether power is on, the power used and the class. Rows are identified by PSE group and port.

From `ALCATEL-IND1-INLINE-POWER-MIB::alaPethPsePortTable`.

- `PowerStatus`: Power status (1 powerOn, 2 powerOff) (`alaPethPsePortPowerStatus`).
- `PowerUsed`: Power used by the port; the MIB states no unit (range 0 to 30000) (`alaPethPsePortPowerActual`).
- `Class`: Power class (0 class0, 1 class1, 2 class2, 3 class3, 4 class4, 5 class5) (`alaPethPsePortPowerClass`).

### Alcatel-Lucent OmniSwitch - Health and Performance Collection

#### OmniSwitch - Device Health (5 minutes)

Device-level CPU and memory utilization averaged over the last minute.

From `ALCATEL-IND1-HEALTH-MIB` scalars.

- `Cpu1MinPct`: CPU utilization averaged over the last minute, percent (`healthDeviceCpu1MinAvg`).
- `Memory1MinPct`: Memory utilization averaged over the last minute, percent (`healthDeviceMemory1MinAvg`).

#### OmniSwitch - Module Health (5 minutes)

CPU, memory and input utilization per module, averaged over the last minute.

From `ALCATEL-IND1-HEALTH-MIB::healthModuleTable`.

- `Cpu1MinPct`: Module CPU utilization over the last minute, percent (`healthModuleCpu1MinAvg`).
- `Memory1MinPct`: Module memory utilization over the last minute, percent (`healthModuleMemory1MinAvg`).
- `Input1MinPct`: Module input utilization over the last minute, percent (`healthModuleRx1MinAvg`).

#### OmniSwitch - Chassis Temperature (5 minutes)

Board and CPU temperature per chassis, and where they sit against the thresholds.

From `ALCATEL-IND1-CHASSIS-MIB::chasChassisTable`.

- `BoardTemperatureC`: Board temperature sensor, degrees Celsius (`chasHardwareBoardTemp`).
- `CpuTemperatureC`: CPU temperature sensor, degrees Celsius (0 where not applicable) (`chasHardwareCpuTemp`).
- `TempRange`: Temperature relative to the thresholds (unknown, notPresent, underThreshold, overFirstThreshold, overDangerThreshold) (`chasTempRange`).

#### OmniSwitch - Stack Chassis (5 minutes)

Each stacked chassis: its role and state, the state of stacking links A and B and the remote slot each reaches, and its stacking mode. Rows are identified by slot.

From `ALCATEL-IND1-STACK-MANAGER-MIB::alaStackMgrChassisTable`.

- `Role`: Chassis role (0 unassigned, 1 primary, 2 secondary, 3 idle, 4 standalone, 5 passthrough) (`alaStackMgrChasRole`).
- `State`: Chassis state (1 running, 2 duplicateSlot, 3 clearedSlot, 4 outOfSlots, 5 outOfTokens, 6 badMix; the published AOS 6 MIB adds 7 inc-Lic, pass-through on a licence mismatch, which TES's revision lacks) (`alaStackMgrChasState`).
- `LinkAState`: Stacking link A state (1 up, 2 down) (`alaStackMgrLocalLinkStateA`).
- `LinkARemoteSlot`: The remote slot seen through link A, 0 if none (`alaStackMgrRemoteNISlotA`).
- `LinkBState`: Stacking link B state (1 up, 2 down) (`alaStackMgrLocalLinkStateB`).
- `LinkBRemoteSlot`: The remote slot seen through link B, 0 if none (`alaStackMgrRemoteNISlotB`).
- `StackingMode`: Operational stacking mode (1 stackable, 2 standalone) (`alaStackMgrOperStackingMode`).

#### OmniSwitch - Stack Links (5 minutes)

Packets and errored packets received and sent on each stacking link, as rates and as counts since the previous poll. Rows are identified by slot and link.

From `ALCATEL-IND1-STACK-MANAGER-MIB::alaStackMgrStatsTable`.

- `PacketsReceived`: Packets received since the previous poll (`delta(alaStackMgrStatPktsRx)`).
- `PacketsReceivedPerSec`: Packets received, per second (`rate(alaStackMgrStatPktsRx)`).
- `PacketsSent`: Packets sent since the previous poll (`delta(alaStackMgrStatPktsTx)`).
- `PacketsSentPerSec`: Packets sent, per second (`rate(alaStackMgrStatPktsTx)`).
- `ErrorsReceived`: Errored packets received since the previous poll (`delta(alaStackMgrStatErrorsRx)`).
- `ErrorsReceivedPerSec`: Errored packets received, per second (`rate(alaStackMgrStatErrorsRx)`).
- `ErrorsSent`: Errored packets sent since the previous poll (`delta(alaStackMgrStatErrorsTx)`).
- `ErrorsSentPerSec`: Errored packets sent, per second (`rate(alaStackMgrStatErrorsTx)`).

`delta(x)` is the change in counter `x` between the last two polls; `rate(x)` is that change divided by the monitor's polling period in seconds (TES's `$pollingSecs`), a per-second rate.

## Expected variations

A table monitor returns no rows when the device lacks the feature or hardware it covers. An empty table does not necessarily indicate a collection failure: first confirm that the feature is present and configured on the device.

- The ERP, VRRP, LAG and PoE monitors can return no rows on a switch where the feature is not configured or the hardware has no PoE.
- LibreNMS's AOS 7 MIB set has no ALCATEL-IND1-INLINE-POWER-MIB, so OmniSwitch - PoE Ports may return no rows on AOS 7 switches (unverified).
- Rows of the ERP port, VRRP and LAG port tables are identified by index values (ring ID and ifIndex, IP version, router ID and ifIndex), which ThirdEye can show as OID digits.
- A switch that is not stacked can return no rows for the stack monitors. LibreNMS's AOS 7 MIB set has no ALCATEL-IND1-STACK-MANAGER-MIB, so they may return no rows on AOS 7 switches (unverified).

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
