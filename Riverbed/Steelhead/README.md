# Riverbed Steelhead Operational and Health and Performance Collections

Last updated: 2026-10-24

Tested against: Not yet validated against a device.

These monitor sets are for: Riverbed Steelhead WAN optimizers.

The folder holds two sets; assign both to the same devices:

- **Riverbed Steelhead - Operational Collection** (2 monitors) covers what the device does (its role's state, load and services).
- **Riverbed Steelhead - Health and Performance Collection** (1 monitor) covers the device itself (CPU, memory, storage and hardware).

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

- `STEELHEAD-MIB`

ThirdEye's built-in MIB store already includes this module, so no MIB import is needed. The MIB files are not included with these collections; to install or update a module, obtain the files as described below and import them with their dependencies.

### Obtaining the Riverbed MIBs

Download the MIB files for the device's software release from Riverbed's support site, and import the modules listed above together with their dependencies.

## Installation

1. In **Inventory**, use **Tools > SNMP System Info** to verify that ThirdEye can query each target device.
2. Go to **Monitors > Sets** and import `Riverbed Steelhead - Operational Collection.3em` and `Riverbed Steelhead - Health and Performance Collection.3em`.
3. Assign **Riverbed Steelhead - Operational Collection** and **Riverbed Steelhead - Health and Performance Collection** to the appropriate devices.
4. Wait at least 1 minute so every monitor has had a collection opportunity, then confirm that current data is present for the applicable monitors.

Automatic assignment to newly discovered devices is disabled in both sets. Review the sets before enabling automatic assignment in your environment.

These host queries match the devices the sets are meant for: `sysObjectID=1.3.6.1.4.1.17163.*`.

## Included monitors

**Riverbed Steelhead - Operational Collection** contains 2 SNMP monitors and **Riverbed Steelhead - Health and Performance Collection** contains 1 SNMP monitor. Metrics carry friendly names; each lists the MIB object it reads or the formula it computes.

### Riverbed Steelhead - Operational Collection

#### Steelhead - Connections (1 minute)

Current optimized, pass-through and total connections.

From `STEELHEAD-MIB` scalars.

- `Optimized`: Current optimized connections (`optimizedConnections`).
- `Active`: Current active (optimized) connections (`activeConnections`).
- `Passthrough`: Current pass-through connections (`passthroughConnections`).
- `Total`: Total connections (`totalConnections`).

#### Steelhead - Optimized Traffic (1 minute)

Optimized traffic on the LAN and WAN sides in each direction, as rates and as volumes since the previous poll. The gap between the LAN and WAN figures is the data reduction.

From `STEELHEAD-MIB` scalars.

- `WanToLanLanBitsPerSec`: WAN to LAN traffic measured on the LAN side, bits per second (`rate(bwAggInLan) * 8`).
- `WanToLanWanBitsPerSec`: WAN to LAN traffic measured on the WAN side, bits per second (`rate(bwAggInWan) * 8`).
- `LanToWanLanBitsPerSec`: LAN to WAN traffic measured on the LAN side, bits per second (`rate(bwAggOutLan) * 8`).
- `LanToWanWanBitsPerSec`: LAN to WAN traffic measured on the WAN side, bits per second (`rate(bwAggOutWan) * 8`).
- `WanToLanLanBytes`: WAN to LAN bytes on the LAN side since the previous poll (`delta(bwAggInLan)`).
- `WanToLanLanBytesPerSec`: WAN to LAN bytes on the LAN side, per second (`rate(bwAggInLan)`).
- `WanToLanWanBytes`: WAN to LAN bytes on the WAN side since the previous poll (`delta(bwAggInWan)`).
- `WanToLanWanBytesPerSec`: WAN to LAN bytes on the WAN side, per second (`rate(bwAggInWan)`).
- `LanToWanLanBytes`: LAN to WAN bytes on the LAN side since the previous poll (`delta(bwAggOutLan)`).
- `LanToWanLanBytesPerSec`: LAN to WAN bytes on the LAN side, per second (`rate(bwAggOutLan)`).
- `LanToWanWanBytes`: LAN to WAN bytes on the WAN side since the previous poll (`delta(bwAggOutWan)`).
- `LanToWanWanBytesPerSec`: LAN to WAN bytes on the WAN side, per second (`rate(bwAggOutWan)`).

### Riverbed Steelhead - Health and Performance Collection

#### Steelhead - System (1 minute)

Overall health, temperature and CPU.

From `STEELHEAD-MIB` scalars.

- `Health`: System health (10000 healthy, 30000 degraded, 31000 admission control, 50000 critical) (`systemHealth`).
- `TemperatureC`: System temperature, degrees Celsius (`systemTemperature`).
- `CpuPct`: CPU utilization across all CPUs, rolling average over the past minute, percent (`cpuUtil1`).

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
