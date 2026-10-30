# F5 BIG-IP Operational and Health and Performance Collections

Last updated: 2026-10-30

Tested against: F5 BIG-IP Virtual Edition 21.1.0.1

These monitor sets are for: F5 BIG-IP appliances and virtual editions running Local Traffic Manager.

The folder holds two sets; assign both to the same devices:

- **F5 BIG-IP - Operational Collection** (8 monitors) covers what the device does (its role's state, load and services).
- **F5 BIG-IP - Health and Performance Collection** (4 monitors) covers the device itself (CPU, memory, storage and hardware).

The collections are a starting point. They define no alert policies or thresholds.

Interface traffic and errors are not included: assign **Standard Network Interfaces - Health and Performance Collection** to the same devices for those.

## Requirements

Before importing the monitor sets, confirm that:

- The device has been added to the ThirdEye inventory.
- ThirdEye can reach the device's SNMP agent, normally over UDP port 161.
- A working SNMP credential has been assigned to the device in ThirdEye. SNMPv3 with authentication and privacy is recommended.
- The BIG-IP SNMP allowed-hosts configuration includes the ThirdEye server or its monitoring subnet.
- The MIB modules listed below are loaded in ThirdEye.

### MIB modules

The collections use these modules:

- `F5-BIGIP-LOCAL-MIB`
- `F5-BIGIP-SYSTEM-MIB`

ThirdEye's built-in MIB store already includes all of these modules, so no MIB import is needed. The MIB files are not included with these collections; to install or update a module, obtain the files as described below and import them with their dependencies.

### Obtaining the F5 MIBs

On a TMOS BIG-IP system, download the enterprise MIB archive directly from the Configuration utility:

1. Open **About**.
2. Select **Downloads**.
3. Select **Download F5 MIBs (`mibs_f5.tar.gz`)**.

The MIB files are also available on the appliance in `/usr/share/snmp/mibs`.

For additional information, see F5's documentation for [downloading enterprise and NET-SNMP MIBs](https://techdocs.f5.com/en-us/bigip-16-0-0/external-monitoring-of-big-ip-systems-implementations/monitoring-big-ip-system-traffic-with-snmp.html).

## Installation

1. In **Inventory**, use **Tools > SNMP System Info** to verify that ThirdEye can query each target device.
2. Go to **Monitors > Sets** and import `F5 BIG-IP - Operational Collection.3em` and `F5 BIG-IP - Health and Performance Collection.3em`.
3. Assign **F5 BIG-IP - Operational Collection** and **F5 BIG-IP - Health and Performance Collection** to the appropriate devices.
4. Wait at least 5 minutes so every monitor has had a collection opportunity, then confirm that current data is present for the applicable monitors.

Automatic assignment to newly discovered devices is disabled in both sets. Review the sets before enabling automatic assignment in your environment.

These host queries match the devices the sets are meant for: `sysObjectID=1.3.6.1.4.1.3375.*`.

## Included monitors

**F5 BIG-IP - Operational Collection** contains 8 SNMP monitors and **F5 BIG-IP - Health and Performance Collection** contains 4 SNMP monitors. Metrics carry friendly names; each lists the MIB object it reads or the formula it computes.

### F5 BIG-IP - Operational Collection

#### F5 BIG-IP - Traffic (5 minutes)

System-wide client and server connections, from the global and the TMM statistics, new connections and client-side throughput.

From `F5-BIGIP-SYSTEM-MIB` scalars.

- `ClientConnections`: Current client-side connections (`sysStatClientCurConns`).
- `ServerConnections`: Current server-side connections (`sysStatServerCurConns`).
- `TmmClientConnections`: Current client-side connections, from the TMM statistics (sysGlobalTmmStat) rather than the global ones (`sysGlobalTmmStatClientCurConns`).
- `TmmServerConnections`: Current server-side connections, from the TMM statistics (`sysGlobalTmmStatServerCurConns`).
- `NewClientConnectionsPerSec`: Client-side connections opened, per second (`rate(sysStatClientTotConns)`).
- `NewClientConnections`: Client-side connections opened since the previous poll (`delta(sysStatClientTotConns)`).
- `ClientBitsInPerSec`: Bytes received from clients, as bits per second (`rate(sysStatClientBytesIn) * 8`).
- `ClientBitsOutPerSec`: Bytes sent to clients, as bits per second (`rate(sysStatClientBytesOut) * 8`).
- `ClientBytesIn`: Bytes received from clients since the previous poll (`delta(sysStatClientBytesIn)`).
- `ClientBytesInPerSec`: Bytes received from clients, per second (`rate(sysStatClientBytesIn)`).
- `ClientBytesOut`: Bytes sent to clients since the previous poll (`delta(sysStatClientBytesOut)`).
- `ClientBytesOutPerSec`: Bytes sent to clients, per second (`rate(sysStatClientBytesOut)`).

#### F5 BIG-IP - TMM Errors (5 minutes)

Packets the traffic management microkernel (TMM) dropped, and connections it could not create for lack of memory, as counts since the previous poll and as rates.

From `F5-BIGIP-SYSTEM-MIB` scalars.

- `DroppedPackets`: Packets dropped since the previous poll (`delta(sysGlobalTmmStatDroppedPackets)`).
- `DroppedPacketsPerSec`: Packets dropped, per second (`rate(sysGlobalTmmStatDroppedPackets)`).
- `ConnectionMemoryErrors`: Connections not created for lack of memory since the previous poll (`delta(sysGlobalTmmStatConnectionMemoryErrors)`).
- `ConnectionMemoryErrorsPerSec`: Connections not created for lack of memory, per second (`rate(sysGlobalTmmStatConnectionMemoryErrors)`).

#### F5 BIG-IP - Failover and Sync (5 minutes)

This unit's failover state and the device group's config sync state, as codes and as the BIG-IP's own status and summary text.

From `F5-BIGIP-SYSTEM-MIB` scalars.

- `FailoverStatus`: Failover status (unknown, offline, forcedOffline, standby, active) (`sysCmFailoverStatusId`).
- `SyncStatus`: Config sync status (unknown, syncing, needManualSync, inSync, syncFailed, syncDisconnected, standalone, awaitingInitialSync, incompatibleVersion, partialSync) (`sysCmSyncStatusId`).
- `FailoverDetail`: Failover status on the system, as text (`sysCmFailoverStatusStatus`).
- `FailoverSummary`: Summary of the failover status, as text (`sysCmFailoverStatusSummary`).
- `SyncDetail`: Config sync status on the system, as text (`sysCmSyncStatusStatus`).
- `SyncSummary`: Summary of the config sync status, as text (`sysCmSyncStatusSummary`).

#### F5 BIG-IP - Traffic Group Status (5 minutes)

Which device is active, standby or offline in each traffic group, the unit of failover in a device group. Rows are per traffic group and device, so an HA pair shows each traffic group once per device; the Device column tells them apart.

From `F5-BIGIP-SYSTEM-MIB::sysCmTrafficGroupStatusTable`, one row per `sysCmTrafficGroupStatusTrafficGroup`.

- `Device`: The device this row describes (`sysCmTrafficGroupStatusDeviceName`).
- `FailoverStatus`: The device's failover status in the traffic group (unknown, offline, forcedOffline, standby, active) (`sysCmTrafficGroupStatusFailoverStatus`).

#### F5 BIG-IP - Virtual Servers (5 minutes)

Availability and enabled state of each virtual server, with the reason the BIG-IP gives for its status.

From `F5-BIGIP-LOCAL-MIB::ltmVsStatusTable`, one row per `ltmVsStatusName`.

- `Availability`: Availability colour (none, green, yellow, red, blue, gray): green is available, red is not (`ltmVsStatusAvailState`).
- `EnabledState`: Enabled state set by the administrator (none, enabled, disabled, disabledbyparent) (`ltmVsStatusEnabledState`).
- `StatusReason`: Detail description of the virtual server's status (`ltmVsStatusDetailReason`).

#### F5 BIG-IP - Virtual Server Traffic (5 minutes)

Client connections and throughput per virtual server.

From `F5-BIGIP-LOCAL-MIB::ltmVirtualServStatTable`, one row per `ltmVirtualServStatName`.

- `ClientConnections`: Current client-side connections (`ltmVirtualServStatClientCurConns`).
- `NewConnectionsPerSec`: Client-side connections opened, per second (`rate(ltmVirtualServStatClientTotConns)`).
- `NewConnections`: Client-side connections opened since the previous poll (`delta(ltmVirtualServStatClientTotConns)`).
- `BitsInPerSec`: Bytes received from clients, as bits per second (`rate(ltmVirtualServStatClientBytesIn) * 8`).
- `BitsOutPerSec`: Bytes sent to clients, as bits per second (`rate(ltmVirtualServStatClientBytesOut) * 8`).
- `BytesIn`: Bytes received from clients since the previous poll (`delta(ltmVirtualServStatClientBytesIn)`).
- `BytesInPerSec`: Bytes received from clients, per second (`rate(ltmVirtualServStatClientBytesIn)`).
- `BytesOut`: Bytes sent to clients since the previous poll (`delta(ltmVirtualServStatClientBytesOut)`).
- `BytesOutPerSec`: Bytes sent to clients, per second (`rate(ltmVirtualServStatClientBytesOut)`).

#### F5 BIG-IP - Pools (5 minutes)

Availability and enabled state of each pool, with the reason the BIG-IP gives for its status.

From `F5-BIGIP-LOCAL-MIB::ltmPoolStatusTable`, one row per `ltmPoolStatusName`.

- `Availability`: Availability colour (none, green, yellow, red, blue, grey): green is available, red is not (`ltmPoolStatusAvailState`).
- `EnabledState`: Enabled state set by the administrator (none, enabled, disabled, disabledbyparent) (`ltmPoolStatusEnabledState`).
- `StatusReason`: Detail description of the pool's status (`ltmPoolStatusDetailReason`).

#### F5 BIG-IP - Pool Members (5 minutes)

Availability and enabled state of each pool member, labelled by its pool, with the member's node name, address type and port, and the reason the BIG-IP gives for its status.

From `F5-BIGIP-LOCAL-MIB::ltmPoolMbrStatusTable`, one row per `ltmPoolMbrStatusPoolName`.

- `Node`: The member's node name (`ltmPoolMbrStatusNodeName`).
- `AddressType`: Type of the member's address (unknown, ipv4, ipv6, ipv4z, ipv6z, dns) (`ltmPoolMbrStatusAddrType`).
- `Port`: The member's service port (`ltmPoolMbrStatusPort`).
- `Availability`: Availability colour (none, green, yellow, red, blue, gray): green is available, red is not (`ltmPoolMbrStatusAvailState`).
- `EnabledState`: Enabled state set by the administrator (`ltmPoolMbrStatusEnabledState`).
- `StatusReason`: Detail description of the pool member's status (`ltmPoolMbrStatusDetailReason`).

### F5 BIG-IP - Health and Performance Collection

#### F5 BIG-IP - CPU and Memory (5 minutes)

Host and traffic management (TMM) CPU usage over the last 1 and 5 minutes, and memory use split into traffic management (TMM) memory and the rest (other) memory, as F5 reports it, and swap use.

From `F5-BIGIP-SYSTEM-MIB` scalars.
Covers the TES built-in template *F5 Big-IP - Memory Utilization*: it polls every object that template polls, under its own names.

- `Cpu1Min`: Host CPU usage ratio over the last minute: busy (user, niced, system) over total time, as the MIB defines it (`sysGlobalHostCpuUsageRatio1m`).
- `Cpu5Min`: Host CPU usage ratio over the last 5 minutes, same definition (`sysGlobalHostCpuUsageRatio5m`).
- `TmmCpu1Min`: Traffic management (TMM) CPU usage ratio over the last minute, as the MIB defines it (`sysGlobalTmmStatTmUsageRatio1m`).
- `TmmCpu5Min`: TMM CPU usage ratio over the last 5 minutes, same definition (`sysGlobalTmmStatTmUsageRatio5m`).
- `OtherMemoryUsedPct`: Memory in use outside TMM as a percentage of the memory outside TMM (`($sysGlobalHostOtherMemoryUsed / $sysGlobalHostOtherMemoryTotal) * 100`).
- `TmmMemoryUsedPct`: TMM memory in use as a percentage of the memory available to TMM (`($sysGlobalTmmStatMemoryUsed / $sysGlobalTmmStatMemoryTotal) * 100`).
- `TotalMemoryUsedPct`: TMM and other memory used, as a percentage of both totals (`(($sysGlobalTmmStatMemoryUsed + $sysGlobalHostOtherMemoryUsed) / ($sysGlobalTmmStatMemoryTotal + $sysGlobalHostOtherMemoryTotal)) * 100`).
- `SwapTotalKB`: Total swap, KB (`sysGlobalHostSwapTotalKb`).
- `SwapUsedKB`: Swap in use, KB (`sysGlobalHostSwapUsedKb`).
- `SwapUsedPct`: Swap in use as a percentage of the total (`($SwapUsedKB / $SwapTotalKB) * 100.0`).

#### F5 BIG-IP - Temperature (5 minutes)

Temperature per chassis sensor, on platforms that report sensor data.

From `F5-BIGIP-SYSTEM-MIB::sysChassisTempTable`.

- `TemperatureC`: Chassis temperature, degrees Celsius (`sysChassisTempTemperature`).

#### F5 BIG-IP - Fans (5 minutes)

Status and speed of each chassis fan, on platforms that report sensor data.

From `F5-BIGIP-SYSTEM-MIB::sysChassisFanTable`.

- `Status`: Fan status (bad, good, notpresent) (`sysChassisFanStatus`).
- `Speed`: Fan speed as reported (0 when unavailable while the fan is good) (`sysChassisFanSpeed`).

#### F5 BIG-IP - Power Supplies (5 minutes)

Status of each power supply, on platforms that report sensor data.

From `F5-BIGIP-SYSTEM-MIB::sysChassisPowerSupplyTable`.

- `Status`: Power supply status (bad, good, notpresent) (`sysChassisPowerSupplyStatus`).

`delta(x)` is the change in counter `x` between the last two polls; `rate(x)` is that change divided by the monitor's polling period in seconds (TES's `$pollingSecs`), a per-second rate.

## Expected variations

A table monitor returns no rows when the device lacks the feature or hardware it covers. An empty table does not necessarily indicate a collection failure: first confirm that the feature is present and configured on the device.

- On a BIG-IP virtual edition, which has no chassis sensors, F5 BIG-IP - Temperature, F5 BIG-IP - Fans and F5 BIG-IP - Power Supplies show No Response rather than empty tables. That is expected there and does not mean the SNMP credential failed: the other monitors return data from the same device.
- HA and config sync values depend on the device service clustering configuration, and traffic group rows on the configured traffic groups.
- Virtual server, pool and pool member tables are empty when those objects do not exist. LTM object names keep their administrative partition, such as `/Common`.

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
4. Check that the BIG-IP SNMP allowed-hosts configuration includes the ThirdEye server.
5. Confirm that the MIB modules listed under Requirements are present in ThirdEye.
6. Wait for the applicable collection interval after making a correction.

If scalar metrics work but a table monitor is empty, confirm that the corresponding feature is configured before treating the result as an error.
