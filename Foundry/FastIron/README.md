# Foundry FastIron Switch Operational and Health and Performance Collections

Last updated: 2026-10-30

Tested against: Not yet validated against a device.

These monitor sets are for: Foundry and Brocade FastIron, BigIron and NetIron switches.

The folder holds two sets; assign both to the same devices:

- **Foundry FastIron Switch - Operational Collection** (9 monitors) covers what the device does (its role's state, load and services).
- **Foundry FastIron Switch - Health and Performance Collection** (7 monitors) covers the device itself (CPU, memory, storage and hardware).

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

- `BROCADE-MCT-CLUSTER-MIB`
- `FOUNDRY-LAG-MIB`
- `FOUNDRY-POE-MIB`
- `FOUNDRY-SN-AGENT-MIB`
- `FOUNDRY-SN-IP-VRRP-MIB`
- `FOUNDRY-SN-MRP-MIB`
- `FOUNDRY-SN-STACKING-MIB`
- `FOUNDRY-SN-VSRP-MIB`

ThirdEye's built-in MIB store already includes all of these modules, so no MIB import is needed. The MIB files are not included with these collections; to install or update a module, obtain the files as described below and import them with their dependencies.

### Obtaining the FastIron MIBs

Download the MIB files for the device's software release from the FastIron vendor's support site, and import the modules listed above together with their dependencies.

## Installation

1. In **Inventory**, use **Tools > SNMP System Info** to verify that ThirdEye can query each target device.
2. Go to **Monitors > Sets** and import `Foundry FastIron Switch - Operational Collection.3em` and `Foundry FastIron Switch - Health and Performance Collection.3em`.
3. Assign **Foundry FastIron Switch - Operational Collection** and **Foundry FastIron Switch - Health and Performance Collection** to the appropriate devices.
4. Wait at least 5 minutes so every monitor has had a collection opportunity, then confirm that current data is present for the applicable monitors.

Automatic assignment to newly discovered devices is disabled in both sets. Review the sets before enabling automatic assignment in your environment.

These host queries match the devices the sets are meant for: `sysObjectID=1.3.6.1.4.1.1991.*`.

## Included monitors

**Foundry FastIron Switch - Operational Collection** contains 9 SNMP monitors and **Foundry FastIron Switch - Health and Performance Collection** contains 7 SNMP monitors. Metrics carry friendly names; each lists the MIB object it reads or the formula it computes.

### Foundry FastIron Switch - Operational Collection

#### Foundry - LAGs (5 minutes)

Each link aggregation group (LAG): its type, deployment state, number of ports, ifIndex and ID. Rows are identified by LAG name, which ThirdEye can show as OID digits.

From `FOUNDRY-LAG-MIB::fdryLinkAggregationGroupTable`.

- `Type`: LAG type (1 static, 2 dynamic, 3 keepalive; the published MIB adds 4 auto and 5 spx, which TES's revision lacks) (`fdryLinkAggregationGroupType`).
- `Deployment`: Deployment state (1 deploy, 2 deployPassive, 3 undeploy, 4 undeployForced; the published MIB adds 5 other, which TES's revision lacks) (`fdryLinkAggregationGroupAdminStatus`).
- `Ports`: Number of ports in the LAG (`fdryLinkAggregationGroupPortCount`).
- `IfIndex`: The LAG's ifIndex, 0 if none (`fdryLinkAggregationGroupIfIndex`).
- `Id`: The LAG's ID (`fdryLinkAggregationGroupId`).

#### Foundry - LACP Ports (5 minutes)

Each port of a dynamic or keepalive LAG: its admin and link status, its LACP status, and the partner's system ID and key. Rows are identified by LAG name and ifIndex.

From `FOUNDRY-LAG-MIB::fdryLinkAggregationGroupLacpPortTable`.

- `AdminStatus`: Admin status (1 up, 2 down, 3 testing) (`fdryLinkAggregationGroupLacpPortAdminStatus`).
- `LinkStatus`: Link status (1 up, 2 down, 3 testing) (`fdryLinkAggregationGroupLacpPortLinkStatus`).
- `LacpStatus`: LACP status (1 operation, 2 down, 3 blocked, 4 inactive, 5 pexforceup) (`fdryLinkAggregationGroupLacpPortLacpStatus`).
- `PartnerSystem`: The LACP partner's system ID (`fdryLinkAggregationGroupLacpPortLacpRemoteSysID`).
- `PartnerKey`: The LACP partner's key (`fdryLinkAggregationGroupLacpPortLacpRemoteKey`).

#### Foundry - VRRP Virtual Routers (5 minutes)

Each VRRP virtual router: its state, current priority and the master's address, with transitions to master and to backup, advertisements of higher priority and priority-zero advertisements from the master, as rates and as counts since the previous poll. Rows are identified by ifIndex and virtual router ID; VRRP-E (VRRP extended) routers are included.

From `FOUNDRY-SN-IP-VRRP-MIB::snVrrpVirRtr2Table`.

- `State`: State (0 init, 1 master, 2 backup) (`snVrrpVirRtr2State`).
- `CurrentPriority`: Current priority (`snVrrpVirRtr2CurrPriority`).
- `MasterAddress`: The master's IP address, from the last advertisement received (`snVrrpVirRtr2MasterIpAddr`).
- `ToMaster`: Transitions to master since the previous poll (`delta(snVrrpVirRtr2TransToMasterStateCnts)`).
- `ToMasterPerSec`: Transitions to master, per second (`rate(snVrrpVirRtr2TransToMasterStateCnts)`).
- `ToBackup`: Transitions to backup since the previous poll (`delta(snVrrpVirRtr2TransToBackupStateCnts)`).
- `ToBackupPerSec`: Transitions to backup, per second (`rate(snVrrpVirRtr2TransToBackupStateCnts)`).
- `HigherPriority`: Advertisements received with a higher priority since the previous poll (`delta(snVrrpVirRtr2RxHigherPriorityCnts)`).
- `HigherPriorityPerSec`: Advertisements received with a higher priority, per second (`rate(snVrrpVirRtr2RxHigherPriorityCnts)`).
- `PriorityZero`: Priority-zero advertisements received from the master since the previous poll (`delta(snVrrpVirRtr2RxPriorityZeroFromMasterCnts)`).
- `PriorityZeroPerSec`: Priority-zero advertisements received from the master, per second (`rate(snVrrpVirRtr2RxPriorityZeroFromMasterCnts)`).

#### Foundry - VSRP Virtual Routers (5 minutes)

Each VSRP virtual router: its state, current priority and the master's address, with transitions to master and to backup, advertisements of higher priority and priority-zero advertisements from the master, as rates and as counts since the previous poll. Rows are identified by VLAN ID and virtual router ID.

From `FOUNDRY-SN-VSRP-MIB::snVsrpVirRtrTable`.

- `State`: State (0 init, 1 master, 2 backup) (`snVsrpVirRtrState`).
- `CurrentPriority`: Current priority (`snVsrpVirRtrCurrPriority`).
- `MasterAddress`: The master's IP address, from the last advertisement received (`snVsrpVirRtrMasterIpAddr`).
- `ToMaster`: Transitions to master since the previous poll (`delta(snVsrpVirRtrTransToMasterStateCnts)`).
- `ToMasterPerSec`: Transitions to master, per second (`rate(snVsrpVirRtrTransToMasterStateCnts)`).
- `ToBackup`: Transitions to backup since the previous poll (`delta(snVsrpVirRtrTransToBackupStateCnts)`).
- `ToBackupPerSec`: Transitions to backup, per second (`rate(snVsrpVirRtrTransToBackupStateCnts)`).
- `HigherPriority`: Advertisements received with a higher priority since the previous poll (`delta(snVsrpVirRtrRxHigherPriorityCnts)`).
- `HigherPriorityPerSec`: Advertisements received with a higher priority, per second (`rate(snVsrpVirRtrRxHigherPriorityCnts)`).
- `PriorityZero`: Priority-zero advertisements received from the master since the previous poll (`delta(snVsrpVirRtrRxPriorityZeroFromMasterCnts)`).
- `PriorityZeroPerSec`: Priority-zero advertisements received from the master, per second (`rate(snVsrpVirRtrRxPriorityZeroFromMasterCnts)`).

#### Foundry - MCT Peers (5 minutes)

Each multi-chassis trunking (MCT) cluster peer: its state, why it is down if it is, and its uptime. Rows are identified by cluster ID and peer address, labelled by the peer's inter-chassis link (ICL) name.

From `BROCADE-MCT-CLUSTER-MIB::brcdMctClusterPeerTable`, one row per `brcdMctClusterPeerIclName`.

- `State`: Peer state (1 unknown, 2 noState, 3 init, 4 ccpUp, 5 ccpDown, 6 reachable) (`brcdMctClusterPeerOperStatus`).
- `DownReason`: Why the peer is down (1 none, 2 loopbackInterfaceDown, 3 iclInterfaceDown, 4 upgradeInProgress, 5 routeNotAvailable, 6 iclVeDown, 7 rBridgeIdMismatch, 8 clusterIdMismatch, 9 keepAliveTimeMismatch, 10 holdTimeMismatch, 11 fastFailoverMismatch, 12 shutdownMesgFromPeer, 13 tcpKeepAliveTimeout, 14 tcpConnCloseMesg, 15 holdTimeoutExpired, 16 sendStateTimeoutExpired, 17 recvStateTimeoutExpired, 18 initMesgSendFail, 19 keepAliveMesgSendFail, 20 invalidAppMesgRecv, 21 badProtocolVersionPktRecv, 22 badPduLengthPktRecv, 23 unknownCcpPktRecv, 24 invalidCcpPktRecv, 25 internalCcpErrorRecv, 26 ccpTcpCommFail) (`brcdMctClusterPeerDownReason`).
- `Uptime`: Time since the peer came up, hundredths of a second, 0 when it is not up (TimeInterval, saved raw) (`brcdMctClusterPeerUpTime`).

#### Foundry - MCT Clients (5 minutes)

Each multi-chassis trunking (MCT) cluster client: its state, whether it is deployed, and its ifIndex. Rows are identified by cluster ID and client name, which ThirdEye can show as OID digits.

From `BROCADE-MCT-CLUSTER-MIB::brcdMctClusterClientTable`.

- `State`: Client state (1 unknown, 2 noState, 3 init, 4 localDeploy, 5 adminUp, 6 remoteUp, 7 localUp, 8 up, 9 slave, 10 master, 11 masterPeerUp) (`brcdMctClusterClientOperStatus`).
- `Deployment`: Deployment state (1 deploy, 2 undeploy) (`brcdMctClusterClientDeploy`).
- `IfIndex`: The client interface's ifIndex (`brcdMctClusterClientIfIndex`).

#### Foundry - Metro Rings (5 minutes)

Each Metro Ring Protocol (MRP) ring: this switch's role, the ring's operational state, the primary and secondary ports' states, and ring health packets sent and received, ring state changes and topology changes received, as rates and as counts since the previous poll. Rows are identified by VLAN ID and ring ID, labelled by ring name.

From `FOUNDRY-SN-MRP-MIB::snMetroRingTable`, one row per `snMetroRingName`.

- `Role`: Ring role (1 other, 2 master, 3 member) (`snMetroRingRole`).
- `OperState`: Operational state (1 other, 2 enabled, 3 disabled) (`snMetroRingOperState`).
- `PrimaryPortState`: Primary port state (1 other, 2 preforwarding, 3 forwarding, 4 blocking, 5 disabled) (`snMetroRingPriPortState`).
- `SecondaryPortState`: Secondary port state (1 other, 2 preforwarding, 3 forwarding, 4 blocking, 5 disabled) (`snMetroRingSecPortState`).
- `HealthSent`: Ring health packets sent since the previous poll (`delta(snMetroRingRHPTransmitted)`).
- `HealthSentPerSec`: Ring health packets sent, per second (`rate(snMetroRingRHPTransmitted)`).
- `HealthReceived`: Ring health packets received since the previous poll (`delta(snMetroRingRHPReceived)`).
- `HealthReceivedPerSec`: Ring health packets received, per second (`rate(snMetroRingRHPReceived)`).
- `StateChanges`: Ring state changes since the previous poll (`delta(snMetroRingStateChanged)`).
- `StateChangesPerSec`: Ring state changes, per second (`rate(snMetroRingStateChanged)`).
- `TopologyChanges`: Topology change packets received since the previous poll (`delta(snMetroRingTCRBPDUReceived)`).
- `TopologyChangesPerSec`: Topology change packets received, per second (`rate(snMetroRingTCRBPDUReceived)`).

#### Foundry - PoE Ports (5 minutes)

Power over Ethernet (PoE) on each port: whether it is enabled, the power it consumes, and the powered device's type. Rows are identified by ifIndex.

From `FOUNDRY-POE-MIB::snAgentPoePortTable`.

- `Control`: Port PoE setting (1 other, 2 disable, 3 enable, 4 enableLegacyDevice) (`snAgentPoePortControl`).
- `ConsumedMw`: Power consumed by the port, milliwatts (`snAgentPoePortConsumed`).
- `DeviceType`: The powered device's type (802.3af, 802.3at or legacy) (`snAgentPoePortType`).

#### Foundry - PoE Units (5 minutes)

Power over Ethernet (PoE) power on each unit: total capacity, unallocated capacity, and allocation requests honored. Rows are identified by unit.

From `FOUNDRY-POE-MIB::snAgentPoeUnitTable`.

- `CapacityMw`: Total PoE capacity, milliwatts (`snAgentPoeUnitPowerCapacityTotal`).
- `FreeMw`: Unallocated PoE capacity, milliwatts (`snAgentPoeUnitPowerCapacityFree`).
- `RequestsHonored`: Allocation requests honored (typed Unsigned32, saved as reported) (`snAgentPoeUnitPowerAllocationsRequestsHonored`).

### Foundry FastIron Switch - Health and Performance Collection

#### Foundry - CPU Utilization (5 minutes)

CPU utilization per slot and CPU, one row per measuring interval (1, 5, 60 and 300 seconds).

From `FOUNDRY-SN-AGENT-MIB::snAgentCpuUtilTable`.
Covers the TES built-in template *Rucks Switch - CPU Utilization*: it polls every object that template polls, under its own names.

- `CpuPct`: CPU utilization, percent (`snAgentCpuUtilPercent`).
- `IntervalSec`: Interval the row's utilization covers, seconds (`snAgentCpuUtilInterval`).

#### Foundry - Memory and Chassis Temperature (5 minutes)

Dynamic memory utilization and chassis temperature.

From `FOUNDRY-SN-AGENT-MIB` scalars.
Covers the TES built-in template *Rucks Switch - Memory Utilization*: it polls every object that template polls, under its own names.

- `MemoryPct`: System dynamic memory utilization, percent (`snAgGblDynMemUtil`).
- `ChassisTemperatureC`: Chassis temperature, degrees Celsius (the MIB counts half degrees) (`$snChasActualTemperature / 2.0`).

#### Foundry - Temperature Sensors (5 minutes)

Temperature per sensor.

From `FOUNDRY-SN-AGENT-MIB::snAgentTempTable`, one row per `snAgentTempSensorDescr`.

- `TemperatureC`: Sensor temperature, degrees Celsius (the MIB counts half degrees) (`$snAgentTempValue / 2.0`).

#### Foundry - Fans (5 minutes)

State of each fan.

From `FOUNDRY-SN-AGENT-MIB::snChasFanTable`, one row per `snChasFanDescription`.

- `Status`: Fan state (other, normal, failure) (`snChasFanOperStatus`).

#### Foundry - Power Supplies (5 minutes)

State of each power supply.

From `FOUNDRY-SN-AGENT-MIB::snChasPwrSupplyTable`, one row per `snChasPwrSupplyDescription`.

- `Status`: Power supply state (other, normal, failure) (`snChasPwrSupplyOperStatus`).

#### Foundry - Stack Units (5 minutes)

Each stack unit: its role, state and priority, both stack ports' states, its left and right neighbor units, and its software version. Rows are identified by unit.

From `FOUNDRY-SN-STACKING-MIB::snStackingOperUnitTable`.

- `Role`: Unit role (1 other, 2 active, 3 standby, 4 member, 5 standalone) (`snStackingOperUnitRole`).
- `State`: Unit state (1 local, 2 remote, 3 reserved, 4 empty) (`snStackingOperUnitState`).
- `Priority`: Unit priority (`snStackingOperUnitPriority`).
- `StackPort1State`: First stack port state (1 other, 2 up, 3 down) (`snStackingOperUnitStackPort1State`).
- `StackPort2State`: Second stack port state (1 other, 2 up, 3 down) (`snStackingOperUnitStackPort2State`).
- `LeftNeighbor`: Left neighbor unit, 0 for none (`snStackingOperUnitNeighbor1`).
- `RightNeighbor`: Right neighbor unit, 0 for none (`snStackingOperUnitNeighbor2`).
- `Version`: The running software image version (`snStackingOperUnitImgVer`).

#### Foundry - Stack (5 minutes)

The stack as a whole: its topology and whether the system is in stacking mode.

From `FOUNDRY-SN-STACKING-MIB` scalars.

- `Topology`: Stack topology (1 other, 2 chain, 3 ring, 4 standalone) (`snStackingGlobalTopology`).
- `Mode`: Stacking mode (1 stackingMode, 2 nonStackingMode) (`snStackingGlobalMode`).

`delta(x)` is the change in counter `x` between the last two polls; `rate(x)` is that change divided by the monitor's polling period in seconds (TES's `$pollingSecs`), a per-second rate.

## Expected variations

A table monitor returns no rows when the device lacks the feature or hardware it covers. An empty table does not necessarily indicate a collection failure: first confirm that the feature is present and configured on the device.

- The LAG, VRRP, VSRP, MCT, Metro Ring and PoE monitors can return no rows on a switch where the feature is not configured or the hardware has no PoE.
- Rows of the LAG, LACP port and MCT client tables are identified by a name, and others by index values (ifIndex, VLAN and router IDs, cluster ID and peer address), which ThirdEye can show as OID digits.
- A switch that is not stacked can return no rows for Foundry - Stack Units.

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
