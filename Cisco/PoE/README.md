# Cisco Catalyst PoE Operational Collection

Last updated: 2026-10-24

Tested against: Catalyst 3850 running IOS XE 16.12.14, with two PSE groups and 96 PoE ports, using SNMPv3 authentication and privacy (over SNMP only)

This monitor set is for: Power over Ethernet on Cisco Catalyst switches: PSE capacity, consumption and budget, and per-port state, faults and power.

The collection is a starting point. It defines no alert policies or thresholds.

Interface traffic and errors are not included: assign **Standard Network Interfaces - Health and Performance Collection** to the same devices for those.

## Requirements

Before importing the monitor set, confirm that:

- The device has been added to the ThirdEye inventory.
- ThirdEye can reach the device's SNMP agent, normally over UDP port 161.
- A working SNMP credential has been assigned to the device in ThirdEye. SNMPv3 with authentication and privacy is recommended.
- The SNMP view permits both `1.3.6.1.2.1.105` and `1.3.6.1.4.1.9.9.402`.
- The switch supports PoE and exposes the required tables; non-PoE Catalyst models are not appropriate targets.
- The MIB modules listed below are loaded in ThirdEye.

### MIB modules

The collection uses these modules:

- `CISCO-POWER-ETHERNET-EXT-MIB`
- `POWER-ETHERNET-MIB`

ThirdEye's built-in MIB store already includes all of these modules. Cisco PoE - PSE Budget and Headroom needs the vendor's current file of `CISCO-POWER-ETHERNET-EXT-MIB` imported with its dependencies: the objects it polls are not in ThirdEye's built-in copies. Without that import those monitors collect nothing; the other monitors are unaffected. The MIB files are not included with this collection; to install or update a module, obtain the files as described below and import them with their dependencies.

### Obtaining the Cisco MIBs

Obtain `POWER-ETHERNET-MIB` and `CISCO-POWER-ETHERNET-EXT-MIB`, with their dependencies, from the [Cisco MIB download site](https://cfnng.cisco.com/mibs) or Cisco's official [MIB repository](https://github.com/cisco/cisco-mibs/tree/main/v2).

## Installation

1. Import the MIB modules listed under Requirements, for the monitors that need them.
2. In **Inventory**, use **Tools > SNMP System Info** to verify that ThirdEye can query each target device.
3. Go to **Monitors > Sets** and import `Cisco Catalyst PoE - Operational Collection.3em`.
4. Assign **Cisco Catalyst PoE - Operational Collection** to the appropriate devices.
5. Wait at least 1 minute so every monitor has had a collection opportunity, then confirm that current data is present for the applicable monitors.

Automatic assignment to newly discovered devices is disabled in the set. Review the set before enabling automatic assignment in your environment.

## Included monitors

The set contains 5 SNMP monitors. Metrics carry friendly names; each lists the MIB object it reads or the formula it computes.

### Cisco PoE - PSE Capacity and Consumption (1 minute)

Power over Ethernet (PoE) per power sourcing equipment (PSE) group: nominal capacity, operational status, measured consumption against it, and the configured usage threshold.

From `POWER-ETHERNET-MIB::pethMainPseTable`.

- `CapacityW`: Nominal PoE capacity, watts (`pethMainPsePower`).
- `OperStatus`: PSE status (1 on, 2 off, 3 faulty) (`pethMainPseOperStatus`).
- `ConsumptionW`: Measured PoE consumption, watts (`pethMainPseConsumptionPower`).
- `ConsumptionPct`: Measured consumption, percent of capacity (`$ConsumptionW * 100.0 / $CapacityW`).
- `UsageThresholdPct`: The switch's configured usage threshold, percent (`pethMainPseUsageThreshold`).

### Cisco PoE - PSE Budget and Headroom (1 minute)

Power over Ethernet (PoE) budget per PSE group as Cisco reports it: used and remaining budget and the share used, with the group's description, physical index and power-monitoring capability. Budget is not measured consumption: on the published module's test switch, 22 W measured was 51.6 W of budget.

From `CISCO-POWER-ETHERNET-EXT-MIB::cpeExtMainPseTable`, one row per `cpeExtMainPseDescr`.
Needs `CISCO-POWER-ETHERNET-EXT-MIB` imported: the objects this monitor polls are not in ThirdEye's built-in MIB store (whose copy of `CISCO-POWER-ETHERNET-EXT-MIB` does not define them), and their OIDs and types are those of the published logicvein-monitors module it ports.

- `UsedBudgetMw`: Used PoE budget, milliwatts (`cpeExtMainPseUsedPower`).
- `RemainingBudgetMw`: Remaining PoE budget, milliwatts (`cpeExtMainPseRemainingPower`).
- `BudgetUsedPct`: Used budget, percent of used plus remaining (`$UsedBudgetMw * 100.0 / ($UsedBudgetMw + $RemainingBudgetMw)`).
- `EntPhysicalIndex`: ENTITY-MIB physical index of the PSE (`cpeExtMainPseEntPhyIndex`).
- `MonitorCapable`: Whether the PSE can monitor power (TruthValue: 1 true, 2 false) (`cpeExtMainPsePwrMonitorCapable`).

### Cisco PoE - Port State (1 minute)

Power over Ethernet (PoE) on each port: whether it is enabled, its detection status, priority and power class. Rows are identified by PSE group and port, which are not ifIndex values. An enabled port with nothing attached reports searching.

From `POWER-ETHERNET-MIB::pethPsePortTable`.

- `AdminEnabled`: Whether PoE is enabled on the port (TruthValue: 1 true, 2 false) (`pethPsePortAdminEnable`).
- `DetectionStatus`: Detection status (1 disabled, 2 searching, 3 deliveringPower, 4 fault, 5 test, 6 otherFault) (`pethPsePortDetectionStatus`).
- `Priority`: Power priority (1 critical, 2 high, 3 low) (`pethPsePortPowerPriority`).
- `PowerClass`: Power class, meaningful while delivering power (1 class0, 2 class1, 3 class2, 4 class3, 5 class4) (`pethPsePortPowerClassifications`).

### Cisco PoE - Port Faults (1 minute)

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

### Cisco PoE - Port Power (1 minute)

Power over Ethernet (PoE) power on each port as Cisco reports it: whether a device is detected, power allocated and available, actual consumption, and the most drawn since the device was powered on, with the port's physical index. Rows are identified by PSE group and port.

From `CISCO-POWER-ETHERNET-EXT-MIB::cpeExtPsePortTable`.

- `DeviceDetected`: Whether a powered device is detected (TruthValue: 1 true, 2 false) (`cpeExtPsePortDeviceDetected`).
- `AllocatedMw`: Power allocated to the device, milliwatts (`cpeExtPsePortPwrAllocated`).
- `AvailableMw`: Power available to the device, milliwatts (`cpeExtPsePortPwrAvailable`).
- `ConsumptionMw`: Actual power consumption, milliwatts (`cpeExtPsePortPwrConsumption`).
- `ConsumptionW`: Actual power consumption, watts (`$ConsumptionMw / 1000.0`).
- `MaxDrawnMw`: Most power drawn since the device was powered on, milliwatts (`cpeExtPsePortMaxPwrDrawn`).
- `EntPhysicalIndex`: ENTITY-MIB physical index of the port; 0 for none (`cpeExtPsePortEntPhyIndex`).

`delta(x)` is the change in counter `x` between the last two polls; `rate(x)` is that change divided by the monitor's polling period in seconds (TES's `$pollingSecs`), a per-second rate.

## Expected variations

A table monitor returns no rows when the device lacks the feature or hardware it covers. An empty table does not necessarily indicate a collection failure: first confirm that the feature is present and configured on the device.

- Assign the PoE collection beside the Cisco IOS Switch collections, which cover the switch's role and health.
- The collection is for PoE-capable switches; a switch without PoE returns nothing for its monitors.
- An enabled port with nothing attached reports searching, not deliveringPower; a port that is not delivering power is not necessarily at fault. The power class is meaningful only while the port delivers power.
- Rows are identified by PSE group and port: port 1 in group 1 and port 1 in group 2 are different ports, and the indexes are not ifIndex values. Cisco PoE - Port Power gives each port's ENTITY-MIB physical index (0 for none), which can be matched to entPhysicalName; stack membership changes can change the indexes.
- Used budget and measured consumption differ: Cisco PoE - PSE Budget and Headroom reports the budget Cisco allocates, Cisco PoE - PSE Capacity and Consumption what the PSE measures.
- Without a current CISCO-POWER-ETHERNET-EXT-MIB imported, Cisco PoE - PSE Budget and Headroom collects nothing for its used and remaining budget.
- The published module's README says it was validated over SNMP only, and that ThirdEye import and scheduled collection have not yet been validated. This collection also differs from it: Port State and Faults is split into Port State and Port Faults, PSE Budget and Headroom is labelled by the PSE description, and percentage and watts columns are added.

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
4. Confirm that the SNMP view permits both `1.3.6.1.2.1.105` and `1.3.6.1.4.1.9.9.402`.
5. If general SNMP works but the PoE tables are empty, check the switch's PoE hardware and its IOS XE release's support for the MIBs; if the standard tables work but the Cisco ones do not, check the extension MIB's support.
6. Compare capacity, allocation, consumption and port state with `show power inline` on the switch, on every stack member.
7. Confirm that the MIB modules listed under Requirements are present in ThirdEye.
8. Wait for the applicable collection interval after making a correction.

If scalar metrics work but a table monitor is empty, confirm that the corresponding feature is configured before treating the result as an error.
