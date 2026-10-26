# Cisco IOS Router Operational and Health and Performance Collections

Last updated: 2026-10-24

Tested against: **Cisco IOS Router - Operational Collection**: not yet validated against a device; **Cisco IOS Router - Health and Performance Collection**: Cisco 2501 and Cisco Catalyst 2950, apart from Cisco - Flash

These monitor sets are for: Cisco ISR, ASR 1000 and other IOS or IOS XE routers.

The folder holds two sets; assign both to the same devices:

- **Cisco IOS Router - Operational Collection** (12 monitors) covers what the device does (its role's state, load and services).
- **Cisco IOS Router - Health and Performance Collection** (7 monitors) covers the device itself (CPU, memory, storage and hardware).

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

- `CES-BGP-DEFAULTS-MIB`
- `CISCO-EIGRP-MIB`
- `CISCO-ENVMON-MIB`
- `CISCO-FLASH-MIB`
- `CISCO-HSRP-MIB`
- `CISCO-IPSEC-FLOW-MONITOR-MIB`
- `CISCO-MEMORY-POOL-MIB`
- `CISCO-PPPOE-MIB`
- `CISCO-PROCESS-MIB`
- `CISCO-RF-MIB`
- `CISCO-RTTMON-MIB`
- `IP-FORWARD-MIB`
- `OLD-CISCO-SYS-MIB`
- `OSPF-MIB`
- `VRRP-MIB`
- `VRRPV3-MIB`

ThirdEye's built-in MIB store already includes all of these modules, so no MIB import is needed. The MIB files are not included with these collections; to install or update a module, obtain the files as described below and import them with their dependencies.

### Obtaining the Cisco MIBs

Download the MIB files from the [Cisco MIB download site](https://cfnng.cisco.com/mibs) or Cisco's official [MIB repository](https://github.com/cisco/cisco-mibs/tree/main/v2), and import the modules listed above together with their dependencies.

## Installation

1. In **Inventory**, use **Tools > SNMP System Info** to verify that ThirdEye can query each target device.
2. Go to **Monitors > Sets** and import `Cisco IOS Router - Operational Collection.3em` and `Cisco IOS Router - Health and Performance Collection.3em`.
3. Assign **Cisco IOS Router - Operational Collection** and **Cisco IOS Router - Health and Performance Collection** to the appropriate devices.
4. Wait at least 15 minutes so every monitor has had a collection opportunity, then confirm that current data is present for the applicable monitors.

Automatic assignment to newly discovered devices is disabled in both sets. Review the sets before enabling automatic assignment in your environment.

These host queries match the devices the sets are meant for: `sysDescr=*Cisco IOS*`.

## Included monitors

**Cisco IOS Router - Operational Collection** contains 12 SNMP monitors and **Cisco IOS Router - Health and Performance Collection** contains 7 SNMP monitors. Metrics carry friendly names; each lists the MIB object it reads or the formula it computes.

### Cisco IOS Router - Operational Collection

#### Cisco - BGP Peers (1 minute)

Each BGP peer's session state, administrative status and remote AS, how long the session has been established, session flaps, and updates in each direction, for routing on BGP. Rows are identified by the peer's address.

From `BGP4-MIB::bgpPeerTable`.

- `State`: Session state (1 idle, 2 connect, 3 active, 4 opensent, 5 openconfirm, 6 established) (`bgpPeerState`).
- `AdminStatus`: Administrative status (1 stop, 2 start) (`bgpPeerAdminStatus`).
- `RemoteAs`: The peer's autonomous system number (`bgpPeerRemoteAs`).
- `EstablishedSecs`: Seconds the session has been established, or since it was last established (`bgpPeerFsmEstablishedTime`).
- `Transitions`: Times the session entered the established state since the previous poll (`delta(bgpPeerFsmEstablishedTransitions)`).
- `TransitionsPerSec`: Times the session entered the established state, per second (`rate(bgpPeerFsmEstablishedTransitions)`).
- `UpdatesIn`: Updates received since the previous poll (`delta(bgpPeerInUpdates)`).
- `UpdatesInPerSec`: Updates received, per second (`rate(bgpPeerInUpdates)`).
- `UpdatesOut`: Updates sent since the previous poll (`delta(bgpPeerOutUpdates)`).
- `UpdatesOutPerSec`: Updates sent, per second (`rate(bgpPeerOutUpdates)`).
- `SinceUpdateInSecs`: Seconds since the last update was received (`bgpPeerInUpdateElapsedTime`).

#### Cisco - OSPF Neighbors (1 minute)

Each OSPF neighbour's adjacency state, router ID and priority, adjacency state changes, and its link-state retransmission queue, for routing on OSPF. Rows are identified by the neighbour's address.

From `OSPF-MIB::ospfNbrTable`.

- `State`: Adjacency state (1 down, 2 attempt, 3 init, 4 twoWay, 5 exchangeStart, 6 exchange, 7 loading, 8 full); 8 full, or 4 twoWay with a non-designated router, is normal (`ospfNbrState`).
- `RouterId`: The neighbour's OSPF router ID (`ospfNbrRtrId`).
- `Priority`: The neighbour's designated-router priority (`ospfNbrPriority`).
- `StateChanges`: Adjacency state changes or errors since the previous poll (`delta(ospfNbrEvents)`).
- `StateChangesPerSec`: Adjacency state changes or errors, per second (`rate(ospfNbrEvents)`).
- `RetransmitQueue`: Link-state advertisements waiting to be retransmitted to the neighbour (`ospfNbrLsRetransQLen`).

#### Cisco - EIGRP Peers (1 minute)

Each EIGRP neighbour's address, interface, hold time and uptime, smooth round-trip and retransmission timeouts, queued packets and retransmissions, for routing on EIGRP. Rows are identified by VPN, AS number and an internal peer handle, since the address column is not text.

From `CISCO-EIGRP-MIB::cEigrpPeerTable`.

- `Address`: The neighbour's address (InetAddress) (`cEigrpPeerAddr`).
- `Interface`: ifIndex of the interface the neighbour is reached on (`cEigrpPeerIfIndex`).
- `HoldSecs`: Hold time remaining before the neighbour is declared down, seconds (`cEigrpHoldTime`).
- `Uptime`: Time since the adjacency was first established, as the router formats it (`cEigrpUpTime`).
- `SrttMs`: Smooth round-trip time, milliseconds (`cEigrpSrtt`).
- `RtoMs`: Retransmission timeout, milliseconds (`cEigrpRto`).
- `Queued`: EIGRP packets queued to be sent to the neighbour (`cEigrpPktsEnqueued`).
- `Retransmissions`: Retransmissions to the neighbour since the previous poll (`delta(cEigrpRetrans)`).
- `RetransmissionsPerSec`: Retransmissions to the neighbour, per second (`rate(cEigrpRetrans)`).

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

#### Cisco IOS - IP SLA Operations (1 minute)

The latest result and round-trip time of each IP SLA operation, with its type, frequency and threshold from the operation's configuration, labelled by the operation's tag. An operation without a tag has an empty label.

From `CISCO-RTTMON-MIB::rttMonLatestRttOperTable`, one row per `rttMonCtrlAdminTag`.

- `Result`: Result of the latest operation (0 other, 1 ok, 2 disconnected, 3 overThreshold, 4 timeout, 5 busy, 6 notConnected, 7 dropped, 8 sequenceError, 9 verifyError, 10 applicationSpecific, 11 dnsServerTimeout, 12 tcpConnectTimeout, 13 httpTransactionTimeout, 14 dnsQueryError, 15 httpError, 16 error, 17 to 20 MPLS LSP errors) (`rttMonLatestRttOperSense`).
- `RoundTripTime`: Round-trip time of the latest operation: milliseconds, or microseconds for a jitter operation set to microsecond precision (`rttMonLatestRttOperCompletionTime`).
- `Type`: Operation type (1 echo, 2 pathEcho, 3 fileIO, 4 script, 5 udpEcho, 6 tcpConnect, 7 http, 8 dns, 9 jitter, 10 dlsw, 11 dhcp, 12 ftp, 13 voip, 14 rtp, 15 lspGroup, 16 icmpjitter, 17 lspPing, 18 lspTrace) (`rttMonCtrlAdminRttType`).
- `FrequencySecs`: Seconds between operations (`rttMonCtrlAdminFrequency`).
- `ThresholdMs`: Round-trip time above which the result is overThreshold, milliseconds (`rttMonCtrlAdminThreshold`).

#### Cisco - Routing Table (1 minute)

Routes in the routing table, from the current and the deprecated IPv4-only count, and valid routes discarded, for the device's routing.

From `IP-FORWARD-MIB` scalars.

- `Routes`: Routes in the routing table, IPv4 and IPv6 (`inetCidrRouteNumber`).
- `Ipv4Routes`: IPv4 routes, from the deprecated count older releases answer (`ipCidrRouteNumber`).
- `RouteDiscards`: Valid routes discarded, for example for lack of memory since the previous poll (`delta(inetCidrRouteDiscards)`).
- `RouteDiscardsPerSec`: Valid routes discarded, for example for lack of memory, per second (`rate(inetCidrRouteDiscards)`).

#### Cisco - IPsec Tunnels (1 minute)

Active IKE and IPsec tunnels and encrypted traffic, for routers and ASAs that terminate VPNs.

From `CISCO-IPSEC-FLOW-MONITOR-MIB` scalars.

- `IkeTunnels`: Active IKE (phase 1) tunnels (`cikeGlobalActiveTunnels`).
- `IpsecTunnels`: Active IPsec (phase 2) tunnels (`cipSecGlobalActiveTunnels`).
- `BitsInPerSec`: Traffic decrypted by all IPsec tunnels, bits per second (`rate(cipSecGlobalHcInOctets) * 8`).
- `BitsOutPerSec`: Traffic encrypted by all IPsec tunnels, bits per second (`rate(cipSecGlobalHcOutOctets) * 8`).
- `BytesIn`: Bytes decrypted by all IPsec tunnels since the previous poll (`delta(cipSecGlobalHcInOctets)`).
- `BytesInPerSec`: Bytes decrypted by all IPsec tunnels, per second (`rate(cipSecGlobalHcInOctets)`).
- `BytesOut`: Bytes encrypted by all IPsec tunnels since the previous poll (`delta(cipSecGlobalHcOutOctets)`).
- `BytesOutPerSec`: Bytes encrypted by all IPsec tunnels, per second (`rate(cipSecGlobalHcOutOctets)`).

#### Cisco - IPsec Errors (1 minute)

IPsec packets dropped in each direction and IKE tunnel setup failures, as rates and as counts since the previous poll.

From `CISCO-IPSEC-FLOW-MONITOR-MIB` scalars.

- `DropsInPerSec`: Inbound IPsec packets dropped, per second (`rate(cipSecGlobalInDrops)`).
- `DropsIn`: Inbound IPsec packets dropped since the previous poll (`delta(cipSecGlobalInDrops)`).
- `DropsOutPerSec`: Outbound IPsec packets dropped, per second (`rate(cipSecGlobalOutDrops)`).
- `DropsOut`: Outbound IPsec packets dropped since the previous poll (`delta(cipSecGlobalOutDrops)`).
- `TunnelFailuresPerSec`: IKE tunnels this device failed to set up, as initiator or responder, per second (`rate(cikeGlobalInitTunnelFails) + rate(cikeGlobalRespTunnelFails)`).
- `TunnelFailures`: IKE tunnels this device failed to set up, as initiator or responder since the previous poll (`delta(cikeGlobalInitTunnelFails) + delta(cikeGlobalRespTunnelFails)`).

#### Cisco IOS - PPPoE Sessions (1 minute)

PPPoE sessions on the router as an access concentrator: current, peak and the configured maximum, and session requests refused for exceeding a limit.

From `CISCO-PPPOE-MIB` scalars.

- `Sessions`: Current PPPoE sessions (`cPppoeSystemCurrSessions`).
- `PeakSessions`: Most PPPoE sessions at one time (`cPppoeSystemHighWaterSessions`).
- `MaxSessions`: Configured maximum PPPoE sessions (`cPppoeSystemMaxAllowedSessions`).
- `ExceededErrors`: PPPoE session requests refused for exceeding a configured limit since the previous poll (`delta(cPppoeSystemExceededSessionErrors)`).
- `ExceededErrorsPerSec`: PPPoE session requests refused for exceeding a configured limit, per second (`rate(cPppoeSystemExceededSessionErrors)`).

#### Cisco - Redundancy (1 minute)

High availability between redundant route processors or supervisors: this unit's and its peer's redundancy state, whether a peer is present, whether manual switchover is inhibited, and why and when the last switchover happened.

From `CISCO-RF-MIB` scalars.

- `UnitState`: This unit's redundancy state (1 notKnown, 2 disabled, 3 initialization, 4 negotiation, 5 standbyCold, 6 standbyColdConfig, 7 standbyColdFileSys, 8 standbyColdBulk, 9 standbyHot, 10 activeFast, 11 activeDrain, 12 activePreconfig, 13 activePostconfig, 14 active, 15 activeExtraload, 16 activeHandback) (`cRFStatusUnitState`).
- `PeerState`: The peer unit's redundancy state, the same values as UnitState (`cRFStatusPeerUnitState`).
- `Duplex`: Whether a redundant peer unit is present (TruthValue: 1 true, 2 false) (`cRFStatusDuplexMode`).
- `SwitchoverInhibited`: Whether manual switchover is inhibited (TruthValue: 1 true, 2 false) (`cRFStatusManualSwactInhibit`).
- `LastSwitchoverReason`: Why the last switchover happened (1 unsupported, 2 none, 3 notKnown, 4 userInitiated, 5 userForced, 6 activeUnitFailed, 7 activeUnitRemoved) (`cRFStatusLastSwactReasonCode`).
- `LastSwitchoverTime`: sysUpTime at the last switchover, hundredths of a second (TimeStamp, saved raw; 0 for none) (`cRFStatusFailoverTime`).

### Cisco IOS Router - Health and Performance Collection

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

#### Cisco IOS - Buffer Failures (1 minute)

Packet buffer allocation failures, a sign of memory pressure or a traffic burst the buffers cannot absorb.

From `OLD-CISCO-SYS-MIB` scalars.

- `FailuresPerSec`: Buffer allocation failures, per second (`rate(bufferFail)`).
- `Failures`: Buffer allocation failures since the previous poll (`delta(bufferFail)`).
- `NoMemoryPerSec`: Buffer creations that failed for lack of free memory, per second (`rate(bufferNoMem)`).
- `NoMemory`: Buffer creations that failed for lack of free memory since the previous poll (`delta(bufferNoMem)`).

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

`delta(x)` is the change in counter `x` between the last two polls; `rate(x)` is that change divided by the monitor's polling period in seconds (TES's `$pollingSecs`), a per-second rate.

## Expected variations

A table monitor returns no rows when the device lacks the feature or hardware it covers. An empty table does not necessarily indicate a collection failure: first confirm that the feature is present and configured on the device.

- The routing protocol (BGP, OSPF, EIGRP), first-hop redundancy (HSRP, VRRP), IP SLA and PPPoE monitors return no rows, or zeros, on a router where the feature is not configured; Cisco - IPsec Tunnels and Cisco - IPsec Errors answered nothing on a Cisco 2911 that terminates no VPNs.
- BGP, OSPF, HSRP and VRRP rows are identified by addresses and interface or group numbers, and EIGRP rows by internal indexes, which ThirdEye can show as OID digits, since those tables have no name column.
- Cisco - Routing Table's Routes and Ipv4Routes come from the current and the deprecated route counts; a release that answers only one leaves the other empty.

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
