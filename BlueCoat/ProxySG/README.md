# Blue Coat ProxySG Operational and Health and Performance Collections

Last updated: 2026-10-24

Tested against: Not yet validated against a device.

These monitor sets are for: Blue Coat ProxySG appliances.

The folder holds two sets; assign both to the same devices:

- **Blue Coat ProxySG - Operational Collection** (2 monitors) covers what the device does (its role's state, load and services).
- **Blue Coat ProxySG - Health and Performance Collection** (5 monitors) covers the device itself (CPU, memory, storage and hardware).

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

- `BLUECOAT-SG-DISK-MIB`
- `BLUECOAT-SG-PROXY-MIB`
- `BLUECOAT-SG-SENSOR-MIB`
- `BLUECOAT-SG-USAGE-MIB`

ThirdEye's built-in MIB store already includes all of these modules, so no MIB import is needed. The MIB files are not included with these collections; to install or update a module, obtain the files as described below and import them with their dependencies.

### Obtaining the Blue Coat MIBs

Download the MIB files for the device's software release from Broadcom's support site for Blue Coat ProxySG, and import the modules listed above together with their dependencies.

## Installation

1. In **Inventory**, use **Tools > SNMP System Info** to verify that ThirdEye can query each target device.
2. Go to **Monitors > Sets** and import `Blue Coat ProxySG - Operational Collection.3em` and `Blue Coat ProxySG - Health and Performance Collection.3em`.
3. Assign **Blue Coat ProxySG - Operational Collection** and **Blue Coat ProxySG - Health and Performance Collection** to the appropriate devices.
4. Wait at least 1 minute so every monitor has had a collection opportunity, then confirm that current data is present for the applicable monitors.

Automatic assignment to newly discovered devices is disabled in both sets. Review the sets before enabling automatic assignment in your environment.

These host queries match the devices the sets are meant for: `sysObjectID=1.3.6.1.4.1.3417.*`.

## Included monitors

**Blue Coat ProxySG - Operational Collection** contains 2 SNMP monitors and **Blue Coat ProxySG - Health and Performance Collection** contains 5 SNMP monitors. Metrics carry friendly names; each lists the MIB object it reads or the formula it computes.

### Blue Coat ProxySG - Operational Collection

#### Blue Coat - HTTP Proxy (1 minute)

HTTP client and server connections, and client requests, hits and errors, as rates and as counts since the previous poll.

From `BLUECOAT-SG-PROXY-MIB` scalars.

- `ClientConnections`: HTTP connections with proxy clients (`sgProxyHttpClientConnections`).
- `ServerConnections`: HTTP connections with remote servers (`sgProxyHttpServerConnections`).
- `RequestsPerSec`: HTTP requests from clients, per second (`rate(sgProxyHttpClientRequests)`).
- `Requests`: HTTP requests from clients since the previous poll (`delta(sgProxyHttpClientRequests)`).
- `Hits`: HTTP hits since the previous poll (`delta(sgProxyHttpClientHits)`).
- `HitsPerSec`: HTTP hits, per second (`rate(sgProxyHttpClientHits)`).
- `Errors`: HTTP errors caused by client connections since the previous poll (`delta(sgProxyHttpClientErrors)`).
- `ErrorsPerSec`: HTTP errors caused by client connections, per second (`rate(sgProxyHttpClientErrors)`).

#### Blue Coat - HTTP Proxy Traffic (1 minute)

HTTP client traffic through the proxy, as bit and byte rates and as bytes since the previous poll.

From `BLUECOAT-SG-PROXY-MIB` scalars.

- `ClientBitsInPerSec`: Bytes received from clients, bits per second (`rate(sgProxyHttpClientInBytes) * 8`).
- `ClientBitsOutPerSec`: Bytes delivered to clients, bits per second (`rate(sgProxyHttpClientOutBytes) * 8`).
- `ClientBytesIn`: Bytes received from clients, bytes since the previous poll (`delta(sgProxyHttpClientInBytes)`).
- `ClientBytesInPerSec`: Bytes received from clients, bytes, per second (`rate(sgProxyHttpClientInBytes)`).
- `ClientBytesOut`: Bytes delivered to clients, bytes since the previous poll (`delta(sgProxyHttpClientOutBytes)`).
- `ClientBytesOutPerSec`: Bytes delivered to clients, bytes, per second (`rate(sgProxyHttpClientOutBytes)`).

### Blue Coat ProxySG - Health and Performance Collection

#### Blue Coat - CPU per Core (1 minute)

Busy time of each CPU core.

From `BLUECOAT-SG-PROXY-MIB::sgProxyCpuCoreTable`.

- `BusyPct`: CPU core busy time, percent (`sgProxyCpuCoreBusyPerCent`).

#### Blue Coat - Memory (1 minute)

Memory pressure on the appliance.

From `BLUECOAT-SG-PROXY-MIB` scalars.

- `MemoryPressurePct`: Memory used by the proxy as a percentage of the total available (`sgProxyMemoryPressure`).

#### Blue Coat - Resource Usage (1 minute)

Use of each monitored resource (such as disk), and whether it is above its high threshold.

From `BLUECOAT-SG-USAGE-MIB::deviceUsageTable`, one row per `deviceUsageName`.

- `UsedPct`: Resource in use, percent (`deviceUsagePercent`).
- `Status`: Usage status (1 ok, 2 high) (`deviceUsageStatus`).

#### Blue Coat - Sensors (1 minute)

Reading and state of each hardware sensor. Value is scaled by 10 to the power Scale, in the sensor's Units.

From `BLUECOAT-SG-SENSOR-MIB::deviceSensorValueTable`, one row per `deviceSensorName`.

- `Value`: Most recent sensor reading, before scaling (`deviceSensorValue`).
- `Scale`: Power of 10 to apply to Value (`deviceSensorScale`).
- `Units`: Units of Value (1 other, 2 truthvalue, 3 specialEnum, 4 volts, 5 celsius, 6 rpm) (`deviceSensorUnits`).
- `Code`: Sensor condition (1 ok; 7 to 12 voltage or temperature warnings; 13 to 15 fan slow or stopped; other values in the MIB) (`deviceSensorCode`).
- `Status`: Sensor operational status (1 ok, 2 unavailable, 3 nonoperational) (`deviceSensorStatus`).

#### Blue Coat - Disks (1 minute)

Status of each disk.

From `BLUECOAT-SG-DISK-MIB::deviceDiskValueTable`.

- `Status`: Disk status (1 present, 2 initializing, 3 inserted, 4 offline, 5 removed, 6 not present, 7 empty, 8 I/O error, 9 unusable, 10 unknown) (`deviceDiskStatus`).

`delta(x)` is the change in counter `x` between the last two polls; `rate(x)` is that change divided by the monitor's polling period in seconds (TES's `$pollingSecs`), a per-second rate.

## Expected variations

A table monitor returns no rows when the device lacks the feature or hardware it covers. An empty table does not necessarily indicate a collection failure: first confirm that the feature is present and configured on the device.

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
