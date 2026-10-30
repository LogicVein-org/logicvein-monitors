# Nokia SR OS Router Operational and Health and Performance Collections

Last updated: 2026-10-30

Tested against: Not yet validated against a device.

These monitor sets are for: Nokia 7750, 7450 and 7950 routers on SR OS.

The folder holds two sets; assign both to the same devices:

- **Nokia SR OS Router - Operational Collection** (1 monitor) covers what the device does (its role's state, load and services).
- **Nokia SR OS Router - Health and Performance Collection** (3 monitors) covers the device itself (CPU, memory, storage and hardware).

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

- `TIMETRA-CHASSIS-MIB`
- `TIMETRA-VRTR-MIB`

ThirdEye's built-in MIB store already includes all of these modules, so no MIB import is needed. The MIB files are not included with these collections; to install or update a module, obtain the files as described below and import them with their dependencies.

### Obtaining the Nokia MIBs

Download the MIB files for the device's software release from Nokia's support portal, and import the modules listed above together with their dependencies.

## Installation

1. In **Inventory**, use **Tools > SNMP System Info** to verify that ThirdEye can query each target device.
2. Go to **Monitors > Sets** and import `Nokia SR OS Router - Operational Collection.3em` and `Nokia SR OS Router - Health and Performance Collection.3em`.
3. Assign **Nokia SR OS Router - Operational Collection** and **Nokia SR OS Router - Health and Performance Collection** to the appropriate devices.
4. Wait at least 5 minutes so every monitor has had a collection opportunity, then confirm that current data is present for the applicable monitors.

Automatic assignment to newly discovered devices is disabled in both sets. Review the sets before enabling automatic assignment in your environment.

These host queries match the devices the sets are meant for: `sysObjectID=1.3.6.1.4.1.6527.*`.

## Included monitors

**Nokia SR OS Router - Operational Collection** contains 1 SNMP monitor and **Nokia SR OS Router - Health and Performance Collection** contains 3 SNMP monitors. Metrics carry friendly names; each lists the MIB object it reads or the formula it computes.

### Nokia SR OS Router - Operational Collection

#### SR OS - Routing Table (5 minutes)

Routes and active interfaces per virtual router (routing instance).

From `TIMETRA-VRTR-MIB::vRtrStatTable`.

- `Routes`: Routes in the routing instance (`vRtrStatCurrNumRoutes`).
- `BgpActiveRoutes`: Active BGP routes (`vRtrBGPActiveRoutes`).
- `ActiveInterfaces`: Router interfaces in service (`vRtrStatActiveIfs`).

### Nokia SR OS Router - Health and Performance Collection

#### SR OS - Component Health (5 minutes)

Temperature against its threshold, and operational state, per hardware component (cards, MDAs, fans, power).

From `TIMETRA-CHASSIS-MIB::tmnxHwTable`, one row per `tmnxHwName`.

- `TemperatureC`: Temperature, degrees Celsius (components without a sensor report a placeholder) (`tmnxHwTemperature`).
- `ThresholdC`: Temperature threshold, degrees Celsius (`tmnxHwTempThreshold`).
- `OperState`: Operational state (inService, outOfService, failed, booting and others) (`tmnxHwOperState`).

#### SR OS - Fans (5 minutes)

Status and speed of each fan tray.

From `TIMETRA-CHASSIS-MIB::tmnxChassisFanTable`.

- `Status`: Fan tray status (deviceStateOk, deviceStateFailed, deviceNotEquipped and others) (`tmnxChassisFanOperStatus`).
- `Speed`: Fan speed (unknown, halfSpeed, fullSpeed) (`tmnxChassisFanSpeed`).

#### SR OS - Power Supplies (5 minutes)

Status of each power supply and its temperature status.

From `TIMETRA-CHASSIS-MIB::tmnxChassisPowerSupplyTable`.

- `Supply1Status`: Status of the first supply (deviceStateOk, deviceStateFailed and others) (`tmnxChassisPowerSupply1Status`).
- `Supply2Status`: Status of the second supply (`tmnxChassisPowerSupply2Status`).
- `TemperatureStatus`: Supply temperature status (`tmnxChassisPowerSupplyTempStatus`).

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
