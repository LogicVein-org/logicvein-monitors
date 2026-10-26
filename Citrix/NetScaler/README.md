# Citrix NetScaler ADC Operational and Health and Performance Collections

Last updated: 2026-10-24

Tested against: Not yet validated against a device.

These monitor sets are for: Citrix NetScaler (ADC) appliances.

The folder holds two sets; assign both to the same devices:

- **Citrix NetScaler ADC - Operational Collection** (1 monitor) covers what the device does (its role's state, load and services).
- **Citrix NetScaler ADC - Health and Performance Collection** (4 monitors) covers the device itself (CPU, memory, storage and hardware).

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

- `NS-ROOT-MIB`

ThirdEye's built-in MIB store already includes this module, so no MIB import is needed. The MIB files are not included with these collections; to install or update a module, obtain the files as described below and import them with their dependencies.

### Obtaining the NetScaler MIBs

Download the MIB files for the device's software release from NetScaler's support site, and import the modules listed above together with their dependencies.

## Installation

1. In **Inventory**, use **Tools > SNMP System Info** to verify that ThirdEye can query each target device.
2. Go to **Monitors > Sets** and import `Citrix NetScaler ADC - Operational Collection.3em` and `Citrix NetScaler ADC - Health and Performance Collection.3em`.
3. Assign **Citrix NetScaler ADC - Operational Collection** and **Citrix NetScaler ADC - Health and Performance Collection** to the appropriate devices.
4. Wait at least 1 minute so every monitor has had a collection opportunity, then confirm that current data is present for the applicable monitors.

Automatic assignment to newly discovered devices is disabled in both sets. Review the sets before enabling automatic assignment in your environment.

These host queries match the devices the sets are meant for: `sysObjectID=1.3.6.1.4.1.5951.*`.

## Included monitors

**Citrix NetScaler ADC - Operational Collection** contains 1 SNMP monitor and **Citrix NetScaler ADC - Health and Performance Collection** contains 4 SNMP monitors. Metrics carry friendly names; each lists the MIB object it reads or the formula it computes.

### Citrix NetScaler ADC - Operational Collection

#### NetScaler - Virtual Servers (1 minute)

State, connections, requests and traffic per virtual server, as rates and as volumes since the previous poll.

From `NS-ROOT-MIB::vserverTable`, one row per `vsvrName`.

- `State`: Server state as an EntityState integer. The MIB text names UP, DOWN, UNKNOWN, OFS (out of service), TROFS and TROFS_DOWN; the numeric codes are not in the TES MIB store or any MIB source at hand, so they are unverified (`vsvrState`).
- `ClientConnections`: Current client connections (`vsvrCurClntConnections`).
- `ServerConnections`: Current server connections (`vsvrCurSrvrConnections`).
- `RequestsPerSec`: Requests received, per second (HTTP and SSL services) (`rate(vsvrTotalRequests)`).
- `Requests`: Requests received since the previous poll (`delta(vsvrTotalRequests)`).
- `BitsInPerSec`: Request bytes received, bits per second (`rate(vsvrTotalRequestBytes) * 8`).
- `BitsOutPerSec`: Response bytes, bits per second (`rate(vsvrTotalResponseBytes) * 8`).
- `BytesIn`: Request bytes received, bytes since the previous poll (`delta(vsvrTotalRequestBytes)`).
- `BytesInPerSec`: Request bytes received, bytes, per second (`rate(vsvrTotalRequestBytes)`).
- `BytesOut`: Response bytes, bytes since the previous poll (`delta(vsvrTotalResponseBytes)`).
- `BytesOutPerSec`: Response bytes, bytes, per second (`rate(vsvrTotalResponseBytes)`).

### Citrix NetScaler ADC - Health and Performance Collection

#### NetScaler - CPU and Memory (1 minute)

System CPU and memory utilization.

From `NS-ROOT-MIB` scalars.

- `CpuPct`: CPU utilization, percent (`resCpuUsage`).
- `MemoryPct`: Memory utilization, percent (`resMemUsage`).

#### NetScaler - CPU per Core (1 minute)

Utilization of each CPU.

From `NS-ROOT-MIB::nsCPUTable`, one row per `nsCPUname`.

- `CpuPct`: CPU utilization, percent (`nsCPUusage`).

#### NetScaler - Health Counters (1 minute)

Voltages, fan speeds and temperatures the appliance reports, by counter name.

From `NS-ROOT-MIB::nsSysHealthTable`, one row per `sysHealthCounterName`.

- `Value`: Counter value: mV for voltages, RPM for fans, degrees Celsius for temperatures (`sysHealthCounterValue`).

#### NetScaler - Disks (1 minute)

Space used on each disk.

From `NS-ROOT-MIB::nsSysHealthDiskTable`, one row per `sysHealthDiskName`.

- `SizeMB`: Total disk space, MB (`sysHealthDiskSize`).
- `UsedMB`: Disk space used, MB (`sysHealthDiskUsed`).
- `UsedPct`: Disk space used as a percentage of the total (`$UsedMB * 100.0 / $SizeMB`).

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
