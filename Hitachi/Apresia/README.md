# Apresia Switch Operational and Health and Performance Collections

Last updated: 2026-10-30

Tested against: Not yet validated against a device.

These monitor sets are for: Apresia switches.

The folder holds two sets; assign both to the same devices:

- **Apresia Switch - Operational Collection** (13 monitors) covers what the device does (its role's state, load and services).
- **Apresia Switch - Health and Performance Collection** (9 monitors) covers the device itself (CPU, memory, storage and hardware).

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

- `APLGM200MIB`
- `APLGM300MIB`
- `Apresia-Series`
- `ApresiaNP-Series`

ThirdEye's built-in MIB store already includes all of these modules, so no MIB import is needed. The MIB files are not included with these collections; to install or update a module, obtain the files as described below and import them with their dependencies.

### Obtaining the APRESIA MIBs

Download the MIB files for the device's software release from APRESIA Systems' support site, and import the modules listed above together with their dependencies.

## Installation

1. In **Inventory**, use **Tools > SNMP System Info** to verify that ThirdEye can query each target device.
2. Go to **Monitors > Sets** and import `Apresia Switch - Operational Collection.3em` and `Apresia Switch - Health and Performance Collection.3em`.
3. Assign **Apresia Switch - Operational Collection** and **Apresia Switch - Health and Performance Collection** to the appropriate devices.
4. Wait at least 5 minutes so every monitor has had a collection opportunity, then confirm that current data is present for the applicable monitors.

Automatic assignment to newly discovered devices is disabled in both sets. Review the sets before enabling automatic assignment in your environment.

These host queries match the devices the sets are meant for: `sysObjectID=1.3.6.1.4.1.278.*`.

## Included monitors

**Apresia Switch - Operational Collection** contains 13 SNMP monitors and **Apresia Switch - Health and Performance Collection** contains 9 SNMP monitors. Metrics carry friendly names; each lists the MIB object it reads or the formula it computes.

### Apresia Switch - Operational Collection

#### Apresia - MLAG (5 minutes)

Multi-chassis link aggregation (MLAG) on AEOS (Apresia-Series) switches: this switch's and the peer's priority and status, by stack unit. Rows are identified by stack unit (chassis) index.

From `Apresia-Series::hclAeosMlagTable`.

- `MyPriority`: This switch's priority (1 first, 2 second) (`hclAeosMlagMyPriority`).
- `PeerPriority`: The peer's priority (1 first, 2 second, 3 unknown) (`hclAeosMlagPeerPriority`).
- `MyStatus`: This switch's MLAG status (1 normal, 2 unreach, 3 abnormalBridgePortDown, 4 abnormalSamePriority, 5 abnormalDomainMismatch, 6 abnormalPeerFailure, 7 abnormalProtocolVersionMismatch, 8 abnormalHelloDownDetect) (`hclAeosMlagMyStatus`).
- `PeerStatus`: The peer's MLAG status (1 normal, 2 unreach, 3 abnormal, 4 unknown) (`hclAeosMlagPeerStatus`).
- `PeerDomain`: The peer's MLAG domain (`hclAeosMlagPeerDomain`).

#### Apresia - MLAG Port Groups (5 minutes)

Each MLAG port group on AEOS (Apresia-Series) switches: this switch's and the peer's status for the group. Rows are identified by stack unit and port group, whose INDEX clause is unverified.

From `Apresia-Series::hclAeosMlagPortGroupTable`.

- `MyStatus`: This switch's status for the group (1 normal, 2 abnormal, 3 down, 4 listening) (`hclAeosMlagPortGroupMyStatus`).
- `PeerStatus`: The peer's status for the group (1 normal, 2 abnormal, 3 down, 4 listening) (`hclAeosMlagPortGroupPeerStatus`).

#### Apresia - MMRP-Plus Ports (5 minutes)

Each port in an MMRP-Plus ring, on AEOS (Apresia-Series) switches: its role in the ring, its status, and its connection status. Rows are identified by index values whose INDEX clause is unverified.

From `Apresia-Series::hclMmrpPlusIfTable`.

- `Mode`: The port's role in the ring (1 unknown, 2 master, 3 slave, 4 divideMaster, 5 divideShadow, 6 aware) (`hclMmrpPlusIfMode`).
- `Status`: Port status (1 unknown, 2 down, 3 failureUp, 4 listening, 5 forwarding, 6 blocking) (`hclMmrpPlusIfStatus`).
- `Connection`: Connection status (1 unknown, 2 normal, 3 abnormal, 4 unreach) (`hclMmrpPlusIfConnection`).

#### Apresia - MMRP-Plus Statistics (5 minutes)

MMRP-Plus control packets on each ring port, on AEOS (Apresia-Series) switches: link-down, link-up and FDB-flush packets received and sent, as rates and as counts since the previous poll. Rows are identified by ifIndex.

From `Apresia-Series::hclMmrpPlusStatsTable`.

- `LinkDownIn`: Link-down packets received since the previous poll (`delta(hclMmrpPlusLinkDownInPkts)`).
- `LinkDownInPerSec`: Link-down packets received, per second (`rate(hclMmrpPlusLinkDownInPkts)`).
- `LinkDownOut`: Link-down packets sent since the previous poll (`delta(hclMmrpPlusLinkDownOutPkts)`).
- `LinkDownOutPerSec`: Link-down packets sent, per second (`rate(hclMmrpPlusLinkDownOutPkts)`).
- `LinkUpIn`: Link-up packets received since the previous poll (`delta(hclMmrpPlusLinkUpInPkts)`).
- `LinkUpInPerSec`: Link-up packets received, per second (`rate(hclMmrpPlusLinkUpInPkts)`).
- `LinkUpOut`: Link-up packets sent since the previous poll (`delta(hclMmrpPlusLinkUpOutPkts)`).
- `LinkUpOutPerSec`: Link-up packets sent, per second (`rate(hclMmrpPlusLinkUpOutPkts)`).
- `FdbFlushIn`: FDB-flush packets received since the previous poll (`delta(hclMmrpPlusFdbFlushInPkts)`).
- `FdbFlushInPerSec`: FDB-flush packets received, per second (`rate(hclMmrpPlusFdbFlushInPkts)`).
- `FdbFlushOut`: FDB-flush packets sent since the previous poll (`delta(hclMmrpPlusFdbFlushOutPkts)`).
- `FdbFlushOutPerSec`: FDB-flush packets sent, per second (`rate(hclMmrpPlusFdbFlushOutPkts)`).

#### Apresia - PoE System (5 minutes)

Each Power over Ethernet (PoE) system block on AEOS (Apresia-Series) switches: whether it is normal. Rows are identified by PoE system block.

From `Apresia-Series::hclAeosPoeSystemTable`.

- `Status`: PoE system status (1 abnormal, 2 normal) (`hclAeosPoeSystemStatus`).

#### Apresia NP - MMRP-Plus Ports (5 minutes)

Each port in an MMRP-Plus ring, on ApresiaNP switches: its role in the ring, its status, and its connection status. Rows are identified by index values whose INDEX clause is unverified.

From `ApresiaNP-Series::npMmrpPlusIfTable`.

- `Mode`: The port's role in the ring (1 unknown, 2 master, 3 slave, 4 divideMaster, 5 divideShadow, 6 aware) (`npMmrpPlusIfMode`).
- `Status`: Port status (1 unknown, 2 down, 3 failureUp, 4 listening, 5 forwarding, 6 blocking) (`npMmrpPlusIfStatus`).
- `Connection`: Connection status (1 unknown, 2 normal, 3 abnormal, 4 unreach) (`npMmrpPlusIfConnection`).

#### Apresia NP - MMRP-Plus Statistics (5 minutes)

MMRP-Plus control packets on each ring port, on ApresiaNP switches: link-down, link-up and FDB-flush packets received and sent, as rates and as counts since the previous poll. Rows are identified by ifIndex.

From `ApresiaNP-Series::npMmrpPlusStatsTable`.

- `LinkDownIn`: Link-down packets received since the previous poll (`delta(npMmrpPlusStatsLinkDownInPkts)`).
- `LinkDownInPerSec`: Link-down packets received, per second (`rate(npMmrpPlusStatsLinkDownInPkts)`).
- `LinkDownOut`: Link-down packets sent since the previous poll (`delta(npMmrpPlusStatsLinkDownOutPkts)`).
- `LinkDownOutPerSec`: Link-down packets sent, per second (`rate(npMmrpPlusStatsLinkDownOutPkts)`).
- `LinkUpIn`: Link-up packets received since the previous poll (`delta(npMmrpPlusStatsLinkUpInPkts)`).
- `LinkUpInPerSec`: Link-up packets received, per second (`rate(npMmrpPlusStatsLinkUpInPkts)`).
- `LinkUpOut`: Link-up packets sent since the previous poll (`delta(npMmrpPlusStatsLinkUpOutPkts)`).
- `LinkUpOutPerSec`: Link-up packets sent, per second (`rate(npMmrpPlusStatsLinkUpOutPkts)`).
- `FdbFlushIn`: FDB-flush packets received since the previous poll (`delta(npMmrpPlusStatsFdbFlushInPkts)`).
- `FdbFlushInPerSec`: FDB-flush packets received, per second (`rate(npMmrpPlusStatsFdbFlushInPkts)`).
- `FdbFlushOut`: FDB-flush packets sent since the previous poll (`delta(npMmrpPlusStatsFdbFlushOutPkts)`).
- `FdbFlushOutPerSec`: FDB-flush packets sent, per second (`rate(npMmrpPlusStatsFdbFlushOutPkts)`).

#### Apresia NP - PoE System (5 minutes)

Each Power over Ethernet (PoE) system block (power supply and controller) on ApresiaNP switches: whether it is normal. Rows are identified by PoE system block.

From `ApresiaNP-Series::npPoeSystemTable`.

- `Status`: PoE system status (1 abnormal, 2 normal) (`npPoeSystemStatus`).

#### Apresia NP - PoE Ports (5 minutes)

Power over Ethernet (PoE) on each port of ApresiaNP switches: its power delivery status, the power it consumes, and the first reason its admin status changed. Rows are identified by port group and port.

From `ApresiaNP-Series::npPoePortTable`.

- `PowerStatus`: Power delivery status (1 delivering, 2 disabled, 3 searching, 4 requesting, 5 stopPowerLimit, 6 stopMpsAbsent, 7 stopPowerDenied, 8 stopOverLoad, 9 stopPowerShort, 10 stopOtherFault) (`npPoePortPowerStatus`).
- `PowerUsed`: Power consumed, 0 when not powered; the MIB description states no unit (`npPoePortConsumptionPower`).
- `AdminStatusCause`: The first reason the admin status changed (1 unknown, 2 cli, 3 snmp, 4 pdMonitoringIcmp, 5 pdMonitoringAcl, 6 pdMonitoringPowerRising, 7 pdMonitoringPowerFalling) (`npPoePortPowerAdminStatusCause`).

#### Apresia GM200 - MMRP-Plus Ports (5 minutes)

Each port in an MMRP-Plus ring, on ApresiaLight GM200 switches: its role in the ring, its status, and its connection status. Rows are identified by index values whose INDEX clause is unverified.

From `APLGM200MIB::gm2MmrpPlusIfTable`.

- `Mode`: The port's role in the ring (1 unknown, 2 master, 3 slave, 4 divideMaster, 5 divideShadow, 6 aware) (`gm2MmrpPlusIfMode`).
- `Status`: Port status (1 unknown, 2 down, 3 failureUp, 4 listening, 5 forwarding) (`gm2MmrpPlusIfStatus`).
- `Connection`: Connection status (1 unknown, 2 normal, 3 abnormal, 4 unreach) (`gm2MmrpPlusIfConnection`).

#### Apresia GM200 - MMRP-Plus Statistics (5 minutes)

MMRP-Plus control packets on each ring port, on ApresiaLight GM200 switches: link-down, link-up and FDB-flush packets received and sent, as rates and as counts since the previous poll. Rows are identified by ifIndex.

From `APLGM200MIB::gm2MmrpPlusStatsTable`.

- `LinkDownIn`: Link-down packets received since the previous poll (`delta(gm2MmrpPlusLinkDownInPkts)`).
- `LinkDownInPerSec`: Link-down packets received, per second (`rate(gm2MmrpPlusLinkDownInPkts)`).
- `LinkDownOut`: Link-down packets sent since the previous poll (`delta(gm2MmrpPlusLinkDownOutPkts)`).
- `LinkDownOutPerSec`: Link-down packets sent, per second (`rate(gm2MmrpPlusLinkDownOutPkts)`).
- `LinkUpIn`: Link-up packets received since the previous poll (`delta(gm2MmrpPlusLinkUpInPkts)`).
- `LinkUpInPerSec`: Link-up packets received, per second (`rate(gm2MmrpPlusLinkUpInPkts)`).
- `LinkUpOut`: Link-up packets sent since the previous poll (`delta(gm2MmrpPlusLinkUpOutPkts)`).
- `LinkUpOutPerSec`: Link-up packets sent, per second (`rate(gm2MmrpPlusLinkUpOutPkts)`).
- `FdbFlushIn`: FDB-flush packets received (the MIB's description of this object repeats HelloF2's; the name says FDB flush) since the previous poll (`delta(gm2MmrpPlusFdbFlushInPkts)`).
- `FdbFlushInPerSec`: FDB-flush packets received (the MIB's description of this object repeats HelloF2's; the name says FDB flush), per second (`rate(gm2MmrpPlusFdbFlushInPkts)`).
- `FdbFlushOut`: FDB-flush packets sent since the previous poll (`delta(gm2MmrpPlusFdbFlushOutPkts)`).
- `FdbFlushOutPerSec`: FDB-flush packets sent, per second (`rate(gm2MmrpPlusFdbFlushOutPkts)`).

#### Apresia GM300 - MMRP-Plus Ports (5 minutes)

Each port in an MMRP-Plus ring, on ApresiaLight GM300 switches: its role in the ring, its status, and its connection status. Rows are identified by index values whose INDEX clause is unverified.

From `APLGM300MIB::gm3MmrpPlusIfTable`.

- `Mode`: The port's role in the ring (1 unknown, 2 master, 3 slave, 4 divideMaster, 5 divideShadow, 6 aware) (`gm3MmrpPlusIfMode`).
- `Status`: Port status (1 unknown, 2 down, 3 failureUp, 4 listening, 5 forwarding) (`gm3MmrpPlusIfStatus`).
- `Connection`: Connection status (1 unknown, 2 normal, 3 abnormal, 4 unreach) (`gm3MmrpPlusIfConnection`).

#### Apresia GM300 - MMRP-Plus Statistics (5 minutes)

MMRP-Plus control packets on each ring port, on ApresiaLight GM300 switches: link-down, link-up and FDB-flush packets received and sent, as rates and as counts since the previous poll. Rows are identified by ifIndex.

From `APLGM300MIB::gm3MmrpPlusStatsTable`.

- `LinkDownIn`: Link-down packets received since the previous poll (`delta(gm3MmrpPlusLinkDownInPkts)`).
- `LinkDownInPerSec`: Link-down packets received, per second (`rate(gm3MmrpPlusLinkDownInPkts)`).
- `LinkDownOut`: Link-down packets sent since the previous poll (`delta(gm3MmrpPlusLinkDownOutPkts)`).
- `LinkDownOutPerSec`: Link-down packets sent, per second (`rate(gm3MmrpPlusLinkDownOutPkts)`).
- `LinkUpIn`: Link-up packets received since the previous poll (`delta(gm3MmrpPlusLinkUpInPkts)`).
- `LinkUpInPerSec`: Link-up packets received, per second (`rate(gm3MmrpPlusLinkUpInPkts)`).
- `LinkUpOut`: Link-up packets sent since the previous poll (`delta(gm3MmrpPlusLinkUpOutPkts)`).
- `LinkUpOutPerSec`: Link-up packets sent, per second (`rate(gm3MmrpPlusLinkUpOutPkts)`).
- `FdbFlushIn`: FDB-flush packets received (the MIB's description of this object repeats HelloF2's; the name says FDB flush) since the previous poll (`delta(gm3MmrpPlusFdbFlushInPkts)`).
- `FdbFlushInPerSec`: FDB-flush packets received (the MIB's description of this object repeats HelloF2's; the name says FDB flush), per second (`rate(gm3MmrpPlusFdbFlushInPkts)`).
- `FdbFlushOut`: FDB-flush packets sent since the previous poll (`delta(gm3MmrpPlusFdbFlushOutPkts)`).
- `FdbFlushOutPerSec`: FDB-flush packets sent, per second (`rate(gm3MmrpPlusFdbFlushOutPkts)`).

### Apresia Switch - Health and Performance Collection

#### Apresia - Fans (5 minutes)

Status of each fan.

From `Apresia-Series::hclFANTable`.

- `Status`: Fan status (1 abnormal, 2 normal, 3 unknown, 4 not running, 5 control error) (`hclFANStatus`).

#### Apresia - Power Supplies (5 minutes)

Status of each power supply and its fan.

From `Apresia-Series::hclPowerTable`.

- `Status`: Power supply status (1 abnormal, 2 normal, 3 unknown, 4 no power module available) (`hclPowerStatus`).
- `FanStatus`: Power supply fan status (1 abnormal, 2 normal, 3 unknown) (`hclPowerFANStatus`).

#### Apresia - CPU (5 minutes)

CPU utilization on AEOS models. The MIB gives no description for these objects, so the values are as the switch reports them.

From `Apresia-Series::hclAeosCpuUtilizationTable`.
Covers the TES built-in template *Apresia Switch(AEOS) - CPU Utilization*: it polls every object that template polls, under its own names.

- `Utilization`: CPU utilization as reported (no MIB description; likely percent) (`hclAeosCpuUtilization`).
- `SampleRate`: The sample rate the utilization is measured over, as reported (no MIB description) (`hclAeosCpuSampleRate`).

#### Apresia NP - Stack (5 minutes)

The ApresiaNP stack as a whole: its topology, this unit's ID, the master and backup master IDs, and the number of units.

From `ApresiaNP-Series` scalars.

- `Topology`: Stack topology (1 standAlone, 2 duplexChain, 3 duplexRing) (`npStackTopology`).
- `MyUnit`: This unit's box ID (`npStackMyBoxId`).
- `Master`: The master's box ID (`npStackMasterId`).
- `BackupMaster`: The backup master's box ID (`npStackBackupMasterId`).
- `Units`: Units in the stack (`npStackBoxCount`).

#### Apresia NP - Stack Units (5 minutes)

Each ApresiaNP stack unit: whether it is present, its priority, and its firmware version. Rows are identified by box ID.

From `ApresiaNP-Series::npStackTable`.

- `Present`: Whether the unit is present (1 exist, 2 unexistent) (`npStackExist`).
- `Priority`: The unit's stacking priority; with preemption disabled the master reads 0, the highest, whatever its configured priority (`npStackPriority`).
- `Version`: The unit's firmware version (`npStackRuntimeVersion`).

#### Apresia NP - Stack Links (5 minutes)

The link state of each ApresiaNP stack unit's two stacking interfaces (SIO1 and SIO2): one port, two ports, or down. Rows are identified by box ID.

From `ApresiaNP-Series::npStackBandwidthTable`.

- `Sio1`: SIO1 link (1 onePort, 2 twoPorts, 3 linkDown, 255 none) (`npStackBandwidthSIO1Act`).
- `Sio2`: SIO2 link (1 onePort, 2 twoPorts, 3 linkDown, 255 none) (`npStackBandwidthSIO2Act`).

#### Apresia NP - CPU (5 minutes)

CPU utilization on ApresiaNP switches, with the period it is calculated over.

From `ApresiaNP-Series::npCpuUtilizationTable`.
Covers the TES built-in template *Apresia NP Series - CPU Utilization*: it polls every object that template polls, under its own names.

- `CpuPct`: CPU utilization, percent (`npCpuUtilizationValue`).
- `SampleSecs`: The period the utilization is calculated over, seconds (`npCpuUtilizationSampleRate`).

#### Apresia GM200 - CPU and Memory (5 minutes)

CPU and memory on ApresiaLight GM200 switches: CPU utilization averaged over 1 and 5 minutes, DRAM used and total, and DRAM and flash utilization.

From `APLGM200MIB` scalars.
Covers the TES built-in templates *ApresiaLight GM200 - CPU Utilization [1min Avg]*, *ApresiaLight GM200 - CPU Utilization [5min Avg]*, *ApresiaLight GM200 - Memory Utilization*: it polls every object they poll, under its own names.

- `Cpu1MinPct`: CPU utilization averaged over 1 minute, percent (`gm2CpuUtilizationIn1min`).
- `Cpu5MinPct`: CPU utilization averaged over 5 minutes, percent (`gm2CpuUtilizationIn5min`).
- `MemoryPct`: DRAM in use, percent of the total (`gm2DramUtilizationPercentage`).
- `MemoryUsedKb`: DRAM in use, kilobytes (`gm2DramUtilizationUsed`).
- `MemoryTotalKb`: Total DRAM, kilobytes (`gm2DramUtilizationTotal`).
- `FlashPct`: Flash memory in use, percent of the total (`gm2FlashUtilizationPercentage`).

#### Apresia GM300 - CPU and Memory (5 minutes)

CPU and memory on ApresiaLight GM300 switches: CPU utilization averaged over 1 and 5 minutes, DRAM used and total, and DRAM and flash utilization.

From `APLGM300MIB` scalars.
Covers the TES built-in templates *ApresiaLight GM300 - CPU Utilization [1min Avg]*, *ApresiaLight GM300 - CPU Utilization [5min Avg]*, *ApresiaLight GM300 - Memory Utilization*: it polls every object they poll, under its own names.

- `Cpu1MinPct`: CPU utilization averaged over 1 minute, percent (`gm3CpuUtilizationIn1min`).
- `Cpu5MinPct`: CPU utilization averaged over 5 minutes, percent (`gm3CpuUtilizationIn5min`).
- `MemoryPct`: DRAM in use, percent of the total (`gm3DramUtilizationPercentage`).
- `MemoryUsedKb`: DRAM in use, kilobytes (`gm3DramUtilizationUsed`).
- `MemoryTotalKb`: Total DRAM, kilobytes (`gm3DramUtilizationTotal`).
- `FlashPct`: Flash memory in use, percent of the total (`gm3FlashUtilizationPercentage`).

`delta(x)` is the change in counter `x` between the last two polls; `rate(x)` is that change divided by the monitor's polling period in seconds (TES's `$pollingSecs`), a per-second rate.

## Expected variations

A table monitor returns no rows when the device lacks the feature or hardware it covers. An empty table does not necessarily indicate a collection failure: first confirm that the feature is present and configured on the device.

- Each series has its own MIB, so the AEOS, NP, GM200 and GM300 monitors return rows only on a switch of that series; the others return none. The MLAG, MMRP-Plus and PoE monitors also need the feature configured or the hardware present.
- No published Apresia MIB file was found, so the row indexes of these tables follow each table's first column and are unverified; rows can show as OID digits.
- Each series has its own MIB, so the AEOS, NP, GM200 and GM300 health monitors return rows only on a switch of that series. The ApresiaNP stack monitors return rows only on ApresiaNP switches that are stacked.

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
