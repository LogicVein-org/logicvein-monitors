# Standard PoE Operational Collection

Last updated: 2026-10-24

Tested against: Not yet validated against a device.

This monitor set is for: Any PoE switch: power over Ethernet capacity and consumption, port state and port faults, from POWER-ETHERNET-MIB.

The collection is a starting point. It defines no alert policies or thresholds.

## Requirements

Before importing the monitor set, confirm that:

- The device has been added to the ThirdEye inventory.
- ThirdEye can reach the device's SNMP agent, normally over UDP port 161.
- A working SNMP credential has been assigned to the device in ThirdEye. SNMPv3 with authentication and privacy is recommended.
- The MIB modules listed below are loaded in ThirdEye.

### MIB modules

The collection uses these modules:

- `POWER-ETHERNET-MIB`

ThirdEye's built-in MIB store already includes this module, so no MIB import is needed. The MIB files are not included with this collection; to install or update a module, obtain the files as described below and import them with their dependencies.

### Obtaining the standard MIBs

`POWER-ETHERNET-MIB` is an IETF standard module, defined in [RFC 3621](https://www.rfc-editor.org/rfc/rfc3621) and published by the RFC Editor.

## Installation

1. In **Inventory**, use **Tools > SNMP System Info** to verify that ThirdEye can query each target device.
2. Go to **Monitors > Sets** and import `Standard PoE - Operational Collection.3em`.
3. Assign **Standard PoE - Operational Collection** to the appropriate devices.
4. Wait at least 1 minute so every monitor has had a collection opportunity, then confirm that current data is present for the applicable monitors.

Automatic assignment to newly discovered devices is disabled in the set. Review the set before enabling automatic assignment in your environment.

## Included monitors

The set contains 3 SNMP monitors. Metrics carry friendly names; each lists the MIB object it reads or the formula it computes.

### PoE - PSE Capacity and Consumption (1 minute)

Power over Ethernet (PoE) per power sourcing equipment (PSE) group: nominal capacity, operational status, measured consumption against it, and the configured usage threshold.

From `POWER-ETHERNET-MIB::pethMainPseTable`.

- `CapacityW`: Nominal PoE capacity, watts (`pethMainPsePower`).
- `OperStatus`: PSE status (1 on, 2 off, 3 faulty) (`pethMainPseOperStatus`).
- `ConsumptionW`: Measured PoE consumption, watts (`pethMainPseConsumptionPower`).
- `ConsumptionPct`: Measured consumption, percent of capacity (`$ConsumptionW * 100.0 / $CapacityW`).
- `UsageThresholdPct`: The switch's configured usage threshold, percent (`pethMainPseUsageThreshold`).

### PoE - Port State (1 minute)

Power over Ethernet (PoE) on each port: whether it is enabled, its detection status, priority and power class. Rows are identified by PSE group and port, which are not ifIndex values. An enabled port with nothing attached reports searching.

From `POWER-ETHERNET-MIB::pethPsePortTable`.

- `AdminEnabled`: Whether PoE is enabled on the port (TruthValue: 1 true, 2 false) (`pethPsePortAdminEnable`).
- `DetectionStatus`: Detection status (1 disabled, 2 searching, 3 deliveringPower, 4 fault, 5 test, 6 otherFault) (`pethPsePortDetectionStatus`).
- `Priority`: Power priority (1 critical, 2 high, 3 low) (`pethPsePortPowerPriority`).
- `PowerClass`: Power class, meaningful while delivering power (1 class0, 2 class1, 3 class2, 4 class3, 5 class4) (`pethPsePortPowerClassifications`).

### PoE - Port Faults (1 minute)

Power over Ethernet (PoE) faults on each port, as counts since the previous poll and as rates: powered devices lost (MPS absent), invalid signatures, power denied, overloads and short circuits. Rows are identified by PSE group and port. An MPS-absent count can mean the device was unplugged.

From `POWER-ETHERNET-MIB::pethPsePortTable`.

- `MpsAbsent`: Times the powered device's maintain-power signature was lost since the previous poll (`delta(pethPsePortMPSAbsentCounter)`).
- `MpsAbsentPerSec`: Times the powered device's maintain-power signature was lost, per second (`rate(pethPsePortMPSAbsentCounter)`).
- `InvalidSignatures`: Invalid powered-device signatures detected since the previous poll (`delta(pethPsePortInvalidSignatureCounter)`).
- `InvalidSignaturesPerSec`: Invalid powered-device signatures detected, per second (`rate(pethPsePortInvalidSignatureCounter)`).
- `PowerDenied`: Times power was denied since the previous poll (`delta(pethPsePortPowerDeniedCounter)`).
- `PowerDeniedPerSec`: Times power was denied, per second (`rate(pethPsePortPowerDeniedCounter)`).
- `Overloads`: Overload conditions since the previous poll (`delta(pethPsePortOverLoadCounter)`).
- `OverloadsPerSec`: Overload conditions, per second (`rate(pethPsePortOverLoadCounter)`).
- `Shorts`: Short-circuit conditions since the previous poll (`delta(pethPsePortShortCounter)`).
- `ShortsPerSec`: Short-circuit conditions, per second (`rate(pethPsePortShortCounter)`).

`delta(x)` is the change in counter `x` between the last two polls; `rate(x)` is that change divided by the monitor's polling period in seconds (TES's `$pollingSecs`), a per-second rate.

## Expected variations

A table monitor returns no rows when the device lacks the feature or hardware it covers. An empty table does not necessarily indicate a collection failure: first confirm that the feature is present and configured on the device.

- Assign it by hand to PoE switches, beside the Standard Switching collection.
- Which standard MIBs a device answers, and how fully, is unverified per vendor: a device that does not implement a table returns nothing for its monitor.
- Do not assign it to a device that takes a Cisco collection polling the same MIBs (Cisco Catalyst PoE), or those objects are polled twice.
- An enabled port with nothing attached reports searching, not deliveringPower. Rows are identified by PSE group and port, which are not ifIndex values.

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
