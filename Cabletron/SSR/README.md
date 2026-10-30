# Cabletron SSR Router Health and Performance Collection

Last updated: 2026-10-30

Tested against: Not yet validated against a device.

This monitor set is for: Cabletron and Enterasys SSR routers.

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

- `CTRON-SSR-HARDWARE-MIB`

ThirdEye's built-in MIB store already includes this module, so no MIB import is needed. The MIB files are not included with this collection; to install or update a module, obtain the files as described below and import them with their dependencies.

### Obtaining the Cabletron MIBs

Download the MIB files for the device's software release from the device vendor's support site, and import the modules listed above together with their dependencies.

## Installation

1. In **Inventory**, use **Tools > SNMP System Info** to verify that ThirdEye can query each target device.
2. Go to **Monitors > Sets** and import `Cabletron SSR Router - Health and Performance Collection.3em`.
3. Assign **Cabletron SSR Router - Health and Performance Collection** to the appropriate devices.
4. Wait at least 5 minutes so every monitor has had a collection opportunity, then confirm that current data is present for the applicable monitors.

Automatic assignment to newly discovered devices is disabled in the set. Review the set before enabling automatic assignment in your environment.

These host queries match the devices the set is meant for: `sysObjectID=1.3.6.1.4.1.52.*`.

## Included monitors

The set contains 1 SNMP monitor. Metrics carry friendly names; each lists the MIB object it reads or the formula it computes.

### Enterasys SSR - Health (5 minutes)

Fan and temperature status of Cabletron SSR routers.

From `CTRON-SSR-HARDWARE-MIB` scalars.

- `FanStatus`: Fan state (working, notWorking, unknown) (`sysHwFan`).
- `TemperatureStatus`: Temperature status (normal, outOfRange, unknown) (`sysHwTemperature`).

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
