# Alaxala AX7700R Router Health and Performance Collection

Last updated: 2026-10-24

Tested against: Not yet validated against a device.

This monitor set is for: Alaxala AX7700R and AX7800R routers.

The collection is a starting point. It defines no alert policies or thresholds.

Interface traffic and errors are not included: assign **Standard Network Interfaces - Health and Performance Collection** to the same devices for those.

## Requirements

Before importing the monitor set, confirm that:

- The device has been added to the ThirdEye inventory.
- ThirdEye can reach the device's SNMP agent, normally over UDP port 161.
- A working SNMP credential has been assigned to the device in ThirdEye. SNMPv3 with authentication and privacy is recommended.
- The MIB modules listed below are loaded in ThirdEye.

### MIB modules

The collection uses these modules:

- `AX7800R`

ThirdEye's built-in MIB store already includes this module, so no MIB import is needed. The MIB files are not included with this collection; to install or update a module, obtain the files as described below and import them with their dependencies.

### Obtaining the ALAXALA MIBs

Download the MIB files for the device's software release from ALAXALA Networks' support site, and import the modules listed above together with their dependencies.

## Installation

1. In **Inventory**, use **Tools > SNMP System Info** to verify that ThirdEye can query each target device.
2. Go to **Monitors > Sets** and import `Alaxala AX7700R Router - Health and Performance Collection.3em`.
3. Assign **Alaxala AX7700R Router - Health and Performance Collection** to the appropriate devices.
4. Wait at least 1 minute so every monitor has had a collection opportunity, then confirm that current data is present for the applicable monitors.

Automatic assignment to newly discovered devices is disabled in the set. Review the set before enabling automatic assignment in your environment.

These host queries match the devices the set is meant for: `sysObjectID=1.3.6.1.4.1.21839.*`.

## Included monitors

The set contains 4 SNMP monitors. Metrics carry friendly names; each lists the MIB object it reads or the formula it computes.

### Alaxala - Control Board (1 minute)

CPU load and memory per basic control unit (BCU) board.

From `AX7800R::ax7700rBcuBoardTable`, one row per `ax7700rBcuBoardName`.

- `Status`: Board status (up, down, closed, disconnect, other) (`ax7700rBcuOperStatus`).
- `Cpu1MinPct`: Processor busy over the last minute, percent (0 when the board is standby) (`ax7700rBcuRmCpuLoad1m`).
- `MemoryUsedKB`: Memory in use, KB (-1 when disconnected) (`ax7700rBcuRmMemoryUsedSize`).
- `MemoryUsedPct`: Memory in use as a percentage of total (`$MemoryUsedKB * 100.0 / $ax7700rBcuRmMemoryTotalSize`).

### Alaxala - Temperature (1 minute)

Temperature state per test point.

From `AX7800R::ax7700rTemperatureStatusTable`, one row per `ax7700rTemperatureStatusDescr`.

- `State`: Temperature state (normal, caution, warning, fatal) (`ax7700rTemperatureState`).
- `Value`: Current measurement of the test point, as reported (`ax7700rTemperatureStatusValue`).

### Alaxala - Fans (1 minute)

Status of each fan.

From `AX7800R::ax7700rFanTable`.

- `Status`: Fan status (ok, under-highspeed-rotation, fault) (`ax7700rFanStatus`).

### Alaxala - Power Supplies (1 minute)

Status of each power supply.

From `AX7800R::ax7700rPowerUnitTable`.

- `Status`: Power supply status (ok, fault; -1 when disconnected) (`ax7700rPowerSupplyStatus`).

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

- The set performs collection only; it does not install alert policies, notification destinations, or incident workflows.
- Counts are changes between polls and rates are per second; alert on those rather than on raw lifetime counters.
- The set contains no SNMP usernames, secrets, device addresses or other environment-specific values.

## Troubleshooting

If no data is collected:

1. Run **Inventory > Tools > SNMP System Info** against the device.
2. Confirm network reachability from ThirdEye to UDP port 161 on the device.
3. Verify the device's SNMP configuration and the credential assigned in ThirdEye.
4. Confirm that the MIB modules listed under Requirements are present in ThirdEye.
5. Wait for the applicable collection interval after making a correction.

If scalar metrics work but a table monitor is empty, confirm that the corresponding feature is configured before treating the result as an error.
