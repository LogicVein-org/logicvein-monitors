# APC UPS Health and Performance Collection

Last updated: 2026-10-24

Tested against: Not yet validated against a device.

This monitor set is for: APC UPSs.

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

- `PowerNet-MIB`

ThirdEye's built-in MIB store already includes this module, so no MIB import is needed. The MIB files are not included with this collection; to install or update a module, obtain the files as described below and import them with their dependencies.

### Obtaining the APC MIBs

Download the MIB files for the device's software release from APC by Schneider Electric's support site, and import the modules listed above together with their dependencies.

## Installation

1. In **Inventory**, use **Tools > SNMP System Info** to verify that ThirdEye can query each target device.
2. Go to **Monitors > Sets** and import `APC UPS - Health and Performance Collection.3em`.
3. Assign **APC UPS - Health and Performance Collection** to the appropriate devices.
4. Wait at least 1 minute so every monitor has had a collection opportunity, then confirm that current data is present for the applicable monitors.

Automatic assignment to newly discovered devices is disabled in the set. Review the set before enabling automatic assignment in your environment.

These host queries match the devices the set is meant for: `sysObjectID=1.3.6.1.4.1.318.*`.

## Included monitors

The set contains 2 SNMP monitors. Metrics carry friendly names; each lists the MIB object it reads or the formula it computes.

### APC UPS - Battery (1 minute)

Battery state, charge, run time, temperature and replacement.

From `PowerNet-MIB` scalars.
Covers the TES built-in template *APC Smart-UPS - BatteryStatus*: it polls every object that template polls, under its own names.

- `Status`: Battery status (1 unknown, 2 normal, 3 low, 4 in fault condition, 5 no battery present) (`upsBasicBatteryStatus`).
- `CapacityPct`: Remaining battery capacity, percent of full (`upsAdvBatteryCapacity`).
- `RunTimeRemaining`: Run time remaining before the battery is exhausted, hundredths of a second (TimeTicks, saved raw) (`upsAdvBatteryRunTimeRemaining`).
- `TemperatureC`: Internal UPS temperature, degrees Celsius (`upsAdvBatteryTemperature`).
- `ReplaceIndicator`: Whether the batteries need replacing (1 no, 2 yes) (`upsAdvBatteryReplaceIndicator`).

### APC UPS - Power (1 minute)

Output state and load, and input and output voltage and frequency.

From `PowerNet-MIB` scalars.
Covers the TES built-in templates *APC Smart-UPS - AverageVoltageInput*, *APC Smart-UPS - AverageVoltageOutput*: it polls every object they poll, under its own names.

- `OutputStatus`: Output status (2 on line, 3 on battery, other values in the MIB) (`upsBasicOutputStatus`).
- `LoadPct`: UPS load, percent of rated capacity (`upsAdvOutputLoad`).
- `InputVoltage`: Utility line voltage, VAC (`upsAdvInputLineVoltage`).
- `OutputVoltage`: Output voltage, VAC (`upsAdvOutputVoltage`).
- `OutputFrequencyHz`: Output frequency, Hz (`upsAdvOutputFrequency`).

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
