# A10 Thunder and AX Operational and Health and Performance Collections

Last updated: 2026-10-30

Tested against: Not yet validated against a device.

These monitor sets are for: A10 Networks Thunder and AX appliances.

The folder holds two sets; assign both to the same devices:

- **A10 Thunder and AX - Operational Collection** (3 monitors) covers what the device does (its role's state, load and services).
- **A10 Thunder and AX - Health and Performance Collection** (3 monitors) covers the device itself (CPU, memory, storage and hardware).

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

- `A10-AX-MIB`

ThirdEye's built-in MIB store already includes this module, so no MIB import is needed. The MIB files are not included with these collections; to install or update a module, obtain the files as described below and import them with their dependencies.

### Obtaining the A10 Networks MIBs

Download the MIB files for the device's software release from A10 Networks' support portal, and import the modules listed above together with their dependencies.

## Installation

1. In **Inventory**, use **Tools > SNMP System Info** to verify that ThirdEye can query each target device.
2. Go to **Monitors > Sets** and import `A10 Thunder and AX - Operational Collection.3em` and `A10 Thunder and AX - Health and Performance Collection.3em`.
3. Assign **A10 Thunder and AX - Operational Collection** and **A10 Thunder and AX - Health and Performance Collection** to the appropriate devices.
4. Wait at least 5 minutes so every monitor has had a collection opportunity, then confirm that current data is present for the applicable monitors.

Automatic assignment to newly discovered devices is disabled in both sets. Review the sets before enabling automatic assignment in your environment.

These host queries match the devices the sets are meant for: `sysObjectID=1.3.6.1.4.1.22610.*`.

## Included monitors

**A10 Thunder and AX - Operational Collection** contains 3 SNMP monitors and **A10 Thunder and AX - Health and Performance Collection** contains 3 SNMP monitors. Metrics carry friendly names; each lists the MIB object it reads or the formula it computes.

### A10 Thunder and AX - Operational Collection

#### A10 - Connections (5 minutes)

System-wide current connections, and new connections as a rate and a count since the previous poll.

From `A10-AX-MIB` scalars.

- `Connections`: Total current connections (`axAppGlobalTotalCurrentConnections`).
- `NewPerSec`: New connections, per second (`rate(axAppGlobalTotalNewConnections)`).
- `New`: New connections since the previous poll (`delta(axAppGlobalTotalNewConnections)`).
- `NewSslPerSec`: New SSL connections, per second (`rate(axAppGlobalTotalSSLConnections)`).
- `NewSsl`: New SSL connections since the previous poll (`delta(axAppGlobalTotalSSLConnections)`).

#### A10 - Virtual Servers (5 minutes)

Status, connections and traffic per virtual server, as rates and as volumes since the previous poll.

From `A10-AX-MIB::axVirtualServerStatTable`, one row per `axVirtualServerStatName`.

- `Status`: Virtual server status (1 up, 2 down, 3 disabled) (`axVirtualServerStatStatus`).
- `Connections`: Current client-side connections (`axVirtualServerStatCurConns`).
- `NewPerSec`: Client-side connections opened, per second (`rate(axVirtualServerStatTotConns)`).
- `New`: Client-side connections opened since the previous poll (`delta(axVirtualServerStatTotConns)`).
- `BitsInPerSec`: Bytes received from clients, bits per second (`rate(axVirtualServerStatBytesIn) * 8`).
- `BitsOutPerSec`: Bytes sent to clients, bits per second (`rate(axVirtualServerStatBytesOut) * 8`).
- `BytesIn`: Bytes received from clients, bytes since the previous poll (`delta(axVirtualServerStatBytesIn)`).
- `BytesInPerSec`: Bytes received from clients, bytes, per second (`rate(axVirtualServerStatBytesIn)`).
- `BytesOut`: Bytes sent to clients, bytes since the previous poll (`delta(axVirtualServerStatBytesOut)`).
- `BytesOutPerSec`: Bytes sent to clients, bytes, per second (`rate(axVirtualServerStatBytesOut)`).

#### A10 - Service Groups (5 minutes)

Status, connections and traffic per service group, as rates and as volumes since the previous poll.

From `A10-AX-MIB::axServiceGroupStatTable`, one row per `axServiceGroupStatName`.

- `Status`: Service group status (1 all up, 2 functional up, 3 partial up, 4 stopped) (`axServiceGroupStatDisplayStatus`).
- `Connections`: Current connections (`axServiceGroupStatCurConns`).
- `BitsInPerSec`: Bytes received, bits per second (`rate(axServiceGroupStatBytesIn) * 8`).
- `BitsOutPerSec`: Bytes sent, bits per second (`rate(axServiceGroupStatBytesOut) * 8`).
- `BytesIn`: Bytes received, bytes since the previous poll (`delta(axServiceGroupStatBytesIn)`).
- `BytesInPerSec`: Bytes received, bytes, per second (`rate(axServiceGroupStatBytesIn)`).
- `BytesOut`: Bytes sent, bytes since the previous poll (`delta(axServiceGroupStatBytesOut)`).
- `BytesOutPerSec`: Bytes sent, bytes, per second (`rate(axServiceGroupStatBytesOut)`).

### A10 Thunder and AX - Health and Performance Collection

#### A10 - System (5 minutes)

CPU, memory and disk use.

From `A10-AX-MIB` scalars.
Covers the TES built-in templates *A10 AX - Disk Utilization*, *A10 AX - Memory Utilization*: it polls every object they poll, under its own names.

- `CpuPct`: Average CPU usage over the last 5 seconds, percent (`axSysAverageCpuUsage`).
- `ControlCpuPct`: Average control CPU usage over the last 5 seconds, percent (`axSysAverageControlCpuUsage`).
- `DataCpuPct`: Average data CPU usage over the last 5 seconds, percent (`axSysAverageDataCpuUsage`).
- `MemoryTotalKB`: Total memory, KB (`axSysMemoryTotal`).
- `MemoryUsedKB`: Memory in use, KB (`axSysMemoryUsage`).
- `MemoryUsedPct`: Memory in use as a percentage of the total (`($MemoryUsedKB / $MemoryTotalKB) * 100.0`).
- `DiskTotalMB`: Total disk space, MB (`axSysDiskTotalSpace`).
- `DiskFreeMB`: Free disk space, MB (`axSysDiskFreeSpace`).
- `DiskUsedPct`: Disk space used as a percentage of the total (`(($DiskTotalMB - $DiskFreeMB) / $DiskTotalMB) * 100.0`).

#### A10 - CPU per Processor (5 minutes)

Usage of each CPU, as the device reports it.

From `A10-AX-MIB::axSysCpuTable`.
Covers the TES built-in template *A10 AX - CPU Utilization*: it polls every object that template polls, under its own names.

- `Usage`: CPU usage value as the device reports it (`axSysCpuUsageValue`).
- `UsageText`: CPU usage as the device reports it in text (`axSysCpuUsage`).

#### A10 - Hardware (5 minutes)

System temperature, fan speeds and power supply status.

From `A10-AX-MIB` scalars.

- `TemperatureC`: Physical system temperature, degrees Celsius (`axSysHwPhySystemTemp`).
- `Fan1Speed`: Fan 1 speed (`axSysHwFan1Speed`).
- `Fan2Speed`: Fan 2 speed (`axSysHwFan2Speed`).
- `Fan3Speed`: Fan 3 speed (`axSysHwFan3Speed`).
- `LowerPowerSupply`: Lower power supply status (-1 unknown, 0 off, 1 on) (`axSysLowerPowerSupplyStatus`).
- `UpperPowerSupply`: Upper power supply status (-1 unknown, 0 off, 1 on) (`axSysUpperPowerSupplyStatus`).

`delta(x)` is the change in counter `x` between the last two polls; `rate(x)` is that change divided by the monitor's polling period in seconds (TES's `$pollingSecs`), a per-second rate.

## Expected variations

A table monitor returns no rows when the device lacks the feature or hardware it covers. An empty table does not necessarily indicate a collection failure: first confirm that the feature is present and configured on the device.

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
