# Cisco IOS Switch Operational and Health and Performance Collections

Last updated: 2026-10-24

Tested against: **Cisco IOS Switch - Operational Collection**: not yet validated against a device; **Cisco IOS Switch - Health and Performance Collection**: Cisco Catalyst 2950, apart from Cisco - Flash and the stack monitors

These monitor sets are for: Cisco Catalyst and other switches on IOS or IOS XE.

The folder holds two sets; assign both to the same devices:

- **Cisco IOS Switch - Operational Collection** (11 monitors) covers what the device does (its role's state, load and services).
- **Cisco IOS Switch - Health and Performance Collection** (9 monitors) covers the device itself (CPU, memory, storage and hardware).

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

- `BRIDGE-MIB`
- `CISCO-ENVMON-MIB`
- `CISCO-ERR-DISABLE-MIB`
- `CISCO-FLASH-MIB`
- `CISCO-HSRP-MIB`
- `CISCO-MEMORY-POOL-MIB`
- `CISCO-PORT-SECURITY-MIB`
- `CISCO-PROCESS-MIB`
- `CISCO-RF-MIB`
- `CISCO-STACKWISE-MIB`
- `CISCO-VIRTUAL-SWITCH-MIB`
- `CISCO-VTP-MIB`
- `ENTITY-MIB`
- `IF-MIB`
- `VRRP-MIB`
- `VRRPV3-MIB`

ThirdEye's built-in MIB store already includes all of these modules, so no MIB import is needed. The MIB files are not included with these collections; to install or update a module, obtain the files as described below and import them with their dependencies.

### Obtaining the Cisco MIBs

Download the MIB files from the [Cisco MIB download site](https://cfnng.cisco.com/mibs) or Cisco's official [MIB repository](https://github.com/cisco/cisco-mibs/tree/main/v2), and import the modules listed above together with their dependencies.

## Installation

1. In **Inventory**, use **Tools > SNMP System Info** to verify that ThirdEye can query each target device.
2. Go to **Monitors > Sets** and import `Cisco IOS Switch - Operational Collection.3em` and `Cisco IOS Switch - Health and Performance Collection.3em`.
3. Assign **Cisco IOS Switch - Operational Collection** and **Cisco IOS Switch - Health and Performance Collection** to the appropriate devices.
4. Wait at least 15 minutes so every monitor has had a collection opportunity, then confirm that current data is present for the applicable monitors.

Automatic assignment to newly discovered devices is disabled in both sets. Review the sets before enabling automatic assignment in your environment.

These host queries match the devices the sets are meant for: `sysDescr=*Catalyst*`, `sysDescr=*C2960*`, `sysDescr=*C3750*`, `sysDescr=*C9300*`.

## Included monitors

**Cisco IOS Switch - Operational Collection** contains 11 SNMP monitors and **Cisco IOS Switch - Health and Performance Collection** contains 9 SNMP monitors. Metrics carry friendly names; each lists the MIB object it reads or the formula it computes.

### Cisco IOS Switch - Operational Collection

#### Cisco - Spanning Tree (1 minute)

Spanning tree topology changes, time since the last change, and this bridge's root, root port, root cost and priority. On Cisco PVST+ switches these scalars answer for the VLAN the SNMP context selects (VLAN 1 by default).

From `BRIDGE-MIB` scalars.

- `TopologyChanges`: Spanning tree topology changes since the previous poll (`delta(dot1dStpTopChanges)`).
- `TopologyChangesPerSec`: Spanning tree topology changes, per second (`rate(dot1dStpTopChanges)`).
- `SinceTopologyChange`: Time since the last topology change, hundredths of a second (TimeTicks, saved raw) (`dot1dStpTimeSinceTopologyChange`).
- `DesignatedRoot`: Bridge ID of the root of the spanning tree (`dot1dStpDesignatedRoot`).
- `RootPort`: Port number of the port with the lowest-cost path toward the root (`dot1dStpRootPort`).
- `RootCost`: Path cost to the root (`dot1dStpRootCost`).
- `Priority`: This bridge's priority (`dot1dStpPriority`).

#### Cisco IOS - VLANs (5 minutes)

Each VLAN's state, type and MTU in the VTP domain, labelled by the VLAN name. Rows are identified by the management domain and the VLAN number.

From `CISCO-VTP-MIB::vtpVlanTable`, one row per `vtpVlanName`.

- `State`: VLAN state (1 operational, 2 suspended, 3 mtuTooBigForDevice, 4 mtuTooBigForTrunk) (`vtpVlanState`).
- `Type`: VLAN type (1 ethernet, 2 fddi, 3 tokenRing, 4 fddiNet, 5 trNet, 6 deprecated) (`vtpVlanType`).
- `Mtu`: VLAN MTU, bytes (`vtpVlanMtu`).

#### Cisco IOS - Port Security (5 minutes)

Port security on each interface: whether it is enabled, its status, secure MAC addresses against the maximum, the action on a violation, and violations, labelled by ifName.

From `CISCO-PORT-SECURITY-MIB::cpsIfConfigTable`, one row per `ifName`.

- `Enabled`: Whether port security is enabled on the interface (TruthValue: 1 true, 2 false) (`cpsIfPortSecurityEnable`).
- `Status`: Port security status (1 secureup, 2 securedown, 3 shutdown) (`cpsIfPortSecurityStatus`).
- `SecureMacs`: Secure MAC addresses on the interface (`cpsIfCurrentSecureMacAddrCount`).
- `MaxSecureMacs`: Most secure MAC addresses the interface allows (`cpsIfMaxSecureMacAddr`).
- `ViolationAction`: Action on a violation (1 shutdown, 2 dropNotify, 3 drop) (`cpsIfViolationAction`).
- `Violations`: Port security violations since the previous poll (`delta(cpsIfViolationCount)`).
- `ViolationsPerSec`: Port security violations, per second (`rate(cpsIfViolationCount)`).

#### Cisco IOS - Err-Disabled Ports (1 minute)

Each interface the switch has error-disabled, why, and when it recovers. Rows exist only while a port is err-disabled, identified by ifIndex and VLAN (0 for the whole interface).

From `CISCO-ERR-DISABLE-MIB::cErrDisableIfStatusTable`.

- `Cause`: Why the port was err-disabled (1 udld, 2 bpduGuard, 3 channelMisconfig, 4 pagpFlap, 5 dtpFlap, 6 linkFlap, 7 l2ptGuard, 8 dot1xSecurityViolation, 9 portSecurityViolation, 10 gbicInvalid, 11 dhcpRateLimit, 12 unicastFlood, 13 vmps, 14 stormControl, 15 inlinePower, 16 arpInspection, 17 portLoopback, 18 packetBuffer, 19 macLimit, 20 linkMonitorFailure, 21 oamRemoteFailure, 22 dot1adIncompEtype, 23 dot1adIncompTunnel, 24 sfpConfigMismatch, 25 communityLimit, 26 invalidPolicy, 27 lsGroup, 28 ekey, 29 portModeFailure, 30 pppoeIaRateLimit, 31 oamRemoteCriticalEvent, 32 oamRemoteDyingGasp, 33 oamRemoteLinkFault, 34 mvrp, 35 tranceiverIncomp) (`cErrDisableIfStatusCause`).
- `RecoverSecs`: Seconds until the switch attempts automatic recovery; 0 when no recovery is scheduled (`cErrDisableIfStatusTimeToRecover`).

#### Cisco - HSRP Groups (1 minute)

Each HSRP group's first-hop redundancy state, priority and preemption, its virtual address, and the active and standby routers. Rows are identified by the interface's ifIndex and the group number.

From `CISCO-HSRP-MIB::cHsrpGrpTable`.

- `State`: HSRP state (1 initial, 2 learn, 3 listen, 4 speak, 5 standby, 6 active) (`cHsrpGrpStandbyState`).
- `Priority`: Configured priority; the highest becomes active (`cHsrpGrpPriority`).
- `Preempt`: Whether a higher-priority router takes over as active (TruthValue: 1 true, 2 false) (`cHsrpGrpPreempt`).
- `VirtualIp`: The group's virtual IP address (`cHsrpGrpVirtualIpAddr`).
- `ActiveRouter`: Address of the router currently active for the group (`cHsrpGrpActiveRouter`).
- `StandbyRouter`: Address of the router currently standby for the group (`cHsrpGrpStandbyRouter`).

#### Cisco - VRRP Routers (1 minute)

Each VRRP virtual router's first-hop redundancy state, administrative state, priority and preemption, with its master and primary addresses. Rows are identified by the interface's ifIndex and the virtual router ID.

From `VRRP-MIB::vrrpOperTable`.

- `State`: VRRP state (1 initialize, 2 backup, 3 master) (`vrrpOperState`).
- `AdminState`: Administrative state (1 up, 2 down) (`vrrpOperAdminState`).
- `Priority`: Priority for the master election; 255 means this router owns the address (`vrrpOperPriority`).
- `Preempt`: Whether a higher-priority backup preempts a lower-priority master (TruthValue: 1 true, 2 false) (`vrrpOperPreemptMode`).
- `MasterIp`: Address of the current master (`vrrpOperMasterIpAddr`).
- `PrimaryIp`: This router's address used as the source of its advertisements (`vrrpOperPrimaryIpAddr`).

#### Cisco - VRRPv3 Routers (1 minute)

Each VRRP version 3 virtual router's first-hop redundancy state, priority, preemption and accept mode, master and primary addresses, advertisement interval and uptime, for IPv4 and IPv6. Rows are identified by the interface's ifIndex, the virtual router ID and the address type; Cisco - VRRP Routers covers VRRP version 2.

From `VRRPV3-MIB::vrrpv3OperationsTable`.

- `State`: Virtual router state (1 initialize, 2 backup, 3 master) (`vrrpv3OperationsStatus`).
- `Priority`: Priority for the master election; 255 means this router owns the address (`vrrpv3OperationsPriority`).
- `Preempt`: Whether a higher-priority virtual router preempts a lower-priority master (TruthValue: 1 true, 2 false) (`vrrpv3OperationsPreemptMode`).
- `AcceptMode`: Whether the master accepts packets addressed to the owner's address (TruthValue: 1 true, 2 false) (`vrrpv3OperationsAcceptMode`).
- `MasterIp`: The master router's real address (InetAddress) (`vrrpv3OperationsMasterIpAddr`).
- `PrimaryIp`: This router's address used as the source of its advertisements (InetAddress) (`vrrpv3OperationsPrimaryIpAddr`).
- `Addresses`: Number of IP addresses associated with the virtual router (`vrrpv3OperationsAddrCount`).
- `AdvertisementSecs`: Seconds between advertisements (`$vrrpv3OperationsAdvInterval / 100.0`).
- `Uptime`: Time since the virtual router left the initialize state, hundredths of a second (TimeTicks, saved raw) (`vrrpv3OperationsUpTime`).

#### Cisco - Redundancy (1 minute)

High availability between redundant route processors or supervisors: this unit's and its peer's redundancy state, whether a peer is present, whether manual switchover is inhibited, and why and when the last switchover happened.

From `CISCO-RF-MIB` scalars.

- `UnitState`: This unit's redundancy state (1 notKnown, 2 disabled, 3 initialization, 4 negotiation, 5 standbyCold, 6 standbyColdConfig, 7 standbyColdFileSys, 8 standbyColdBulk, 9 standbyHot, 10 activeFast, 11 activeDrain, 12 activePreconfig, 13 activePostconfig, 14 active, 15 activeExtraload, 16 activeHandback) (`cRFStatusUnitState`).
- `PeerState`: The peer unit's redundancy state, the same values as UnitState (`cRFStatusPeerUnitState`).
- `Duplex`: Whether a redundant peer unit is present (TruthValue: 1 true, 2 false) (`cRFStatusDuplexMode`).
- `SwitchoverInhibited`: Whether manual switchover is inhibited (TruthValue: 1 true, 2 false) (`cRFStatusManualSwactInhibit`).
- `LastSwitchoverReason`: Why the last switchover happened (1 unsupported, 2 none, 3 notKnown, 4 userInitiated, 5 userForced, 6 activeUnitFailed, 7 activeUnitRemoved) (`cRFStatusLastSwactReasonCode`).
- `LastSwitchoverTime`: sysUpTime at the last switchover, hundredths of a second (TimeStamp, saved raw; 0 for none) (`cRFStatusFailoverTime`).

#### Cisco IOS - Virtual Switch (1 minute)

Virtual switching (VSS or StackWise Virtual): whether the switch runs as a virtual switch pair, its domain and switch ID, and the dual-active detection details.

From `CISCO-VIRTUAL-SWITCH-MIB` scalars.

- `Mode`: Virtual switch mode (1 standalone, 2 multiNode) (`cvsSwitchMode`).
- `Domain`: Virtual switch domain identifier (`cvsDomain`).
- `SwitchId`: This switch's ID in the virtual switch domain (`cvsSwitchID`).
- `DualActiveDetection`: Details of the last dual-active detection, as the switch reports it (`cvsDualActiveDetectionInformation`).

#### Cisco IOS - Virtual Switch Chassis (1 minute)

Each chassis of a virtual switch pair and its role, active or standby, labelled by the chassis' entPhysicalName.

From `CISCO-VIRTUAL-SWITCH-MIB::cvsChassisTable`, one row per `entPhysicalName`.

- `SwitchId`: The chassis' switch ID (`cvsChassisSwitchID`).
- `Role`: The chassis' role (1 standalone, 2 active, 3 standby) (`cvsChassisRole`).

#### Cisco IOS - Virtual Switch Links (1 minute)

Each virtual switch link (VSL) between the pair: its state, the switch it forms, and configured against operational ports, labelled by the link's port channel ifName.

From `CISCO-VIRTUAL-SWITCH-MIB::cvsVSLConnectionTable`, one row per `ifName`.

- `State`: Link state (1 up, 2 down) (`cvsVSLConnectOperStatus`).
- `SwitchId`: ID of the switch the link forms (`cvsVSLCoreSwitchID`).
- `ConfiguredPorts`: Ports configured for the link (`cvsVSLConfiguredPortCount`).
- `OperationalPorts`: Ports operational in the link (`cvsVSLOperationalPortCount`).

### Cisco IOS Switch - Health and Performance Collection

#### Cisco - CPU Utilization (1 minute)

CPU busy percentage per processor over 5 seconds, 1 minute and 5 minutes.

From `CISCO-PROCESS-MIB::cpmCPUTotalTable`.
Covers the TES built-in templates *Cisco ASA - CPU Utilization [1min Avg]*, *Cisco ASA - CPU Utilization [5min Avg]*, *Cisco Nexus - CPU Utilization [1min Avg]*, *Cisco Nexus - CPU Utilization [5min Avg]*: it polls every object they poll, under its own names. Despite their names, the Cisco ASA and Nexus templates named here poll the newer Rev objects, as this monitor does; Cisco ASA - CPU Utilization keeps the original objects, which an ASA 5505 on software 8.2 answers.

- `Busy5SecPct`: CPU busy over the last 5 seconds, percent (`cpmCPUTotal5secRev`).
- `Busy1MinPct`: CPU busy over the last minute (exponentially decayed average), percent (`cpmCPUTotal1minRev`).
- `Busy5MinPct`: CPU busy over the last 5 minutes (exponentially decayed average), percent (`cpmCPUTotal5minRev`).

#### Cisco - Memory Pools (1 minute)

Used and free memory per pool (processor, I/O) on classic IOS, and the largest free block, which shows fragmentation.

From `CISCO-MEMORY-POOL-MIB::ciscoMemoryPoolTable`, one row per `ciscoMemoryPoolName`.

- `UsedBytes`: Bytes in use (`ciscoMemoryPoolUsed`).
- `FreeBytes`: Bytes free (`ciscoMemoryPoolFree`).
- `LargestFreeBytes`: Largest contiguous free block, bytes (`ciscoMemoryPoolLargestFree`).
- `UsedPct`: Used as a percentage of used plus free (`$UsedBytes * 100 / ($UsedBytes + $FreeBytes)`).

#### Cisco - Temperature (1 minute)

Temperature per sensor against the sensor's configured threshold, and its state.

From `CISCO-ENVMON-MIB::ciscoEnvMonTemperatureStatusTable`, one row per `ciscoEnvMonTemperatureStatusDescr`.

- `TemperatureC`: Current temperature, degrees Celsius (`ciscoEnvMonTemperatureStatusValue`).
- `ThresholdC`: The sensor's configured threshold, degrees Celsius (`ciscoEnvMonTemperatureThreshold`).
- `State`: Sensor state (normal, warning, critical, shutdown, notPresent, notFunctioning) (`ciscoEnvMonTemperatureState`).

#### Cisco - Fan Status (1 minute)

State of each fan or fan tray.

From `CISCO-ENVMON-MIB::ciscoEnvMonFanStatusTable`, one row per `ciscoEnvMonFanStatusDescr`.

- `State`: Fan state (normal, warning, critical, shutdown, notPresent, notFunctioning) (`ciscoEnvMonFanState`).

#### Cisco - Power Supply Status (1 minute)

State of each power supply.

From `CISCO-ENVMON-MIB::ciscoEnvMonSupplyStatusTable`, one row per `ciscoEnvMonSupplyStatusDescr`.

- `State`: Power supply state (normal, warning, critical, shutdown, notPresent, notFunctioning) (`ciscoEnvMonSupplyState`).

#### Cisco - Flash (15 minutes)

Size, free space and use of each flash partition, with its file count and status, labelled by the partition name. Above 4,294,967,295 bytes the 32-bit size objects report that maximum and only the 64-bit ones are right, so UsedPct is computed from the 64-bit objects and UsedPct32 from the 32-bit ones, for releases that answer only those.

From `CISCO-FLASH-MIB::ciscoFlashPartitionTable`, one row per `ciscoFlashPartitionName`.

- `SizeBytes`: Partition size, bytes, 32-bit: reports 4,294,967,295 for a larger partition (`ciscoFlashPartitionSize`).
- `FreeBytes`: Free space in the partition, bytes, 32-bit: reports 4,294,967,295 when more is free (`ciscoFlashPartitionFreeSpace`).
- `SizeBytesExtended`: Partition size, bytes, 64-bit (CounterBasedGauge64) (`ciscoFlashPartitionSizeExtended`).
- `FreeBytesExtended`: Free space in the partition, bytes, 64-bit (CounterBasedGauge64) (`ciscoFlashPartitionFreeSpaceExtended`).
- `UsedPct`: Space used, percent of the partition, from the 64-bit objects (`($SizeBytesExtended - $FreeBytesExtended) * 100.0 / $SizeBytesExtended`).
- `UsedPct32`: Space used, percent of the partition, from the 32-bit objects: wrong above 4,294,967,295 bytes (`($SizeBytes - $FreeBytes) * 100.0 / $SizeBytes`).
- `Files`: Files in the partition (`ciscoFlashPartitionFileCount`).
- `Status`: Partition status (1 readOnly, 2 runFromFlash, 3 readWrite) (`ciscoFlashPartitionStatus`).

#### Cisco IOS - Stack Members (1 minute)

Each StackWise stack member's switch number, role, state and priorities, and its software image, labelled by the member's entPhysicalName.

From `CISCO-STACKWISE-MIB::cswSwitchInfoTable`, one row per `entPhysicalName`.

- `Number`: The member's current switch number (`cswSwitchNumCurrent`).
- `Role`: Role in the stack (1 master, 2 member, 3 notMember, 4 standby) (`cswSwitchRole`).
- `State`: Member state (1 waiting, 2 progressing, 3 added, 4 ready, 5 sdmMismatch, 6 verMismatch, 7 featureMismatch, 8 newMasterInit, 9 provisioned, 10 invalid, 11 removed) (`cswSwitchState`).
- `SwPriority`: Configured priority for the master election (`cswSwitchSwPriority`).
- `HwPriority`: Hardware priority for the master election (`cswSwitchHwPriority`).
- `SoftwareImage`: The member's software image (`cswSwitchSoftwareImage`).

#### Cisco IOS - Stack Ports (1 minute)

Each StackWise stack port's state and the member on its far end, labelled by ifName.

From `CISCO-STACKWISE-MIB::cswStackPortInfoTable`, one row per `ifName`.

- `State`: Stack port state (1 up, 2 down, 3 forcedDown) (`cswStackPortOperStatus`).
- `Neighbor`: entPhysicalIndex of the member on the far end; 0 for none (`cswStackPortNeighbor`).

#### Cisco IOS - Stack Ring (1 minute)

Whether the StackWise ring is redundant (closed), and the most members the stack can hold.

From `CISCO-STACKWISE-MIB` scalars.

- `RingRedundant`: Whether the stack ring is redundant (TruthValue: 1 true, 2 false) (`cswRingRedundant`).
- `MaxMembers`: Most members the stack can hold (`cswMaxSwitchNum`).

`delta(x)` is the change in counter `x` between the last two polls; `rate(x)` is that change divided by the monitor's polling period in seconds (TES's `$pollingSecs`), a per-second rate.

## Expected variations

A table monitor returns no rows when the device lacks the feature or hardware it covers. An empty table does not necessarily indicate a collection failure: first confirm that the feature is present and configured on the device.

- Port security, HSRP, VRRP and virtual switching monitors return no rows on a switch where the feature is not configured; Cisco IOS - Err-Disabled Ports has rows only while a port is err-disabled.
- On Cisco PVST+ switches, Cisco - Spanning Tree answers for the VLAN the SNMP context selects, VLAN 1 by default; the other VLANs' spanning trees are not reached by this set.
- Cisco - Redundancy matters on a switch with redundant supervisors or a virtual switch pair.

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
