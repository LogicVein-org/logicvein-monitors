# APC Rack PDU Health and Performance Collection

Last updated: 2026-10-24

Tested against: Not yet validated against a device.

This monitor set is for: APC rack PDUs (attach by hand).

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
2. Go to **Monitors > Sets** and import `APC Rack PDU - Health and Performance Collection.3em`.
3. Assign **APC Rack PDU - Health and Performance Collection** to the appropriate devices.
4. Wait at least 1 minute so every monitor has had a collection opportunity, then confirm that current data is present for the applicable monitors.

Automatic assignment to newly discovered devices is disabled in the set. Review the set before enabling automatic assignment in your environment.

## Included monitors

The set contains 10 SNMP monitors. Metrics carry friendly names; each lists the MIB object it reads or the formula it computes.

### APC Rack PDU - Power Supplies (1 minute)

Status of the rack PDU's two power supplies.

From `PowerNet-MIB` scalars.

- `PowerSupply1`: Power supply 1 status (1 ok, 2 failed) (`rPDUPowerSupply1Status`).
- `PowerSupply2`: Power supply 2 status (1 ok, 2 failed, 3 not present) (`rPDUPowerSupply2Status`).

### APC Rack PDU - Device Status (1 minute)

Each rack PDU (rPDU2 branch, APC's newer rack PDUs; one row per PDU in a daisy chain): load state, power, apparent power, power factor, energy and power supply alarms. Rows are labelled by the PDU's user-defined name.

From `PowerNet-MIB::rPDU2DeviceStatusTable`, one row per `rPDU2DeviceStatusName`.

- `LoadState`: Load state (1 low load, 2 normal, 3 near overload, 4 overload, 5 not supported) (`rPDU2DeviceStatusLoadState`).
- `PowerW`: Power, watts (from hundredths of a kilowatt; negative when the model does not support it, from the MIB's -1) (`$rPDU2DeviceStatusPower * 10`).
- `PeakPowerW`: Peak power since the last peak reset, watts (from hundredths of a kilowatt; negative when the model does not support it, from the MIB's -1) (`$rPDU2DeviceStatusPeakPower * 10`).
- `ApparentPowerVA`: Apparent power, volt-amperes (from hundredths of a kVA; negative when the model does not support it, from the MIB's -1) (`$rPDU2DeviceStatusApparentPower * 10`).
- `PowerFactor`: Power factor (from hundredths; negative when the model does not support it, from the MIB's -1) (`$rPDU2DeviceStatusPowerFactor / 100.0`).
- `EnergyKWh`: Energy since the last meter reset, kilowatt-hours (from tenths; negative when the model does not support it, from the MIB's -1) (`$rPDU2DeviceStatusEnergy / 10.0`).
- `PowerSupplyAlarm`: Power supply alarm (1 normal, 2 alarm) (`rPDU2DeviceStatusPowerSupplyAlarm`).
- `PowerSupply1`: Power supply 1 (1 normal, 2 alarm, 3 not installed) (`rPDU2DeviceStatusPowerSupply1Status`).
- `PowerSupply2`: Power supply 2 (1 normal, 2 alarm, 3 not installed) (`rPDU2DeviceStatusPowerSupply2Status`).

### APC Rack PDU - Phase Load (1 minute)

Each input phase of each rack PDU (rPDU2 branch): load state, current, peak current, voltage, power, apparent power and power factor. Rows are identified by PDU and phase number.

From `PowerNet-MIB::rPDU2PhaseStatusTable`.

- `Module`: The PDU's user-defined numeric ID (`rPDU2PhaseStatusModule`).
- `Phase`: Phase number (`rPDU2PhaseStatusNumber`).
- `LoadState`: Load state (1 low load, 2 normal, 3 near overload, 4 overload) (`rPDU2PhaseStatusLoadState`).
- `CurrentA`: Current, amperes (from tenths of an ampere) (`$rPDU2PhaseStatusCurrent / 10.0`).
- `PeakCurrentA`: Peak current since the last peak reset, amperes (from tenths of an ampere) (`$rPDU2PhaseStatusPeakCurrent / 10.0`).
- `VoltageV`: Voltage, volts (-1 when the model does not support it) (`rPDU2PhaseStatusVoltage`).
- `PowerW`: Power, watts (from hundredths of a kilowatt; negative when the model does not support it, from the MIB's -1) (`$rPDU2PhaseStatusPower * 10`).
- `ApparentPowerVA`: Apparent power, volt-amperes (from hundredths of a kVA; negative when the model does not support it, from the MIB's -1) (`$rPDU2PhaseStatusApparentPower * 10`).
- `PowerFactor`: Power factor (from hundredths; negative when the model does not support it, from the MIB's -1) (`$rPDU2PhaseStatusPowerFactor / 100.0`).

### APC Rack PDU - Bank Load (1 minute)

Each breaker bank of each rack PDU (rPDU2 branch): load state, current and peak current. Rows are identified by PDU and bank number.

From `PowerNet-MIB::rPDU2BankStatusTable`.

- `Module`: The PDU's user-defined numeric ID (`rPDU2BankStatusModule`).
- `Bank`: Bank number (`rPDU2BankStatusNumber`).
- `LoadState`: Load state (1 low load, 2 normal, 3 near overload, 4 overload) (`rPDU2BankStatusLoadState`).
- `CurrentA`: Current, amperes (from tenths of an ampere) (`$rPDU2BankStatusCurrent / 10.0`).
- `PeakCurrentA`: Peak current since the last peak reset, amperes (from tenths of an ampere) (`$rPDU2BankStatusPeakCurrent / 10.0`).

### APC Rack PDU - Switched Outlets (1 minute)

Each switched outlet (rPDU2 branch): whether it is on. Rows are labelled by the outlet's user-defined name.

From `PowerNet-MIB::rPDU2OutletSwitchedStatusTable`, one row per `rPDU2OutletSwitchedStatusName`.

- `Module`: The PDU's user-defined numeric ID (`rPDU2OutletSwitchedStatusModule`).
- `Outlet`: Outlet number (`rPDU2OutletSwitchedStatusNumber`).
- `State`: Outlet state (1 off, 2 on) (`rPDU2OutletSwitchedStatusState`).

### APC Rack PDU - Metered Outlets (1 minute)

Each metered outlet (rPDU2 branch): load state, current, power and energy. Rows are labelled by the outlet's user-defined name. PowerNet-MIB does not state the unit of the power columns, so they are saved as the PDU reports them.

From `PowerNet-MIB::rPDU2OutletMeteredStatusTable`, one row per `rPDU2OutletMeteredStatusName`.

- `Module`: The PDU's user-defined numeric ID (`rPDU2OutletMeteredStatusModule`).
- `Outlet`: Outlet number (`rPDU2OutletMeteredStatusNumber`).
- `LoadState`: Load state (1 low load, 2 normal, 3 near overload, 4 overload) (`rPDU2OutletMeteredStatusState`).
- `CurrentA`: Current, amperes (from tenths of an ampere) (`$rPDU2OutletMeteredStatusCurrent / 10.0`).
- `Power`: Power draw as the PDU reports it (unit not stated in the MIB) (`rPDU2OutletMeteredStatusPower`).
- `PeakPower`: Peak power draw as the PDU reports it (unit not stated in the MIB) (`rPDU2OutletMeteredStatusPeakPower`).
- `EnergyKWh`: Energy since the last meter reset, kilowatt-hours (from tenths) (`$rPDU2OutletMeteredStatusEnergy / 10.0`).

### APC Rack PDU - Device Power (rPDU) (1 minute)

The rack PDU's power and apparent power (rPDU branch, APC's earlier rack PDUs). The branch's power factor is not polled: PowerNet-MIB describes it as a setting (writable on AP7xxx models), not a measurement.

From `PowerNet-MIB` scalars.

- `PowerW`: Power, watts (`rPDUIdentDevicePowerWatts`).
- `ApparentPowerVA`: Apparent power, volt-amperes (`rPDUIdentDevicePowerVA`).

### APC Rack PDU - Phase and Bank Load (rPDU) (1 minute)

Each phase and breaker bank (rPDU branch): load state and current. Phase rows come first; a phase row has bank number 0.

From `PowerNet-MIB::rPDULoadStatusTable`.

- `Phase`: Phase number (`rPDULoadStatusPhaseNumber`).
- `Bank`: Bank number (0 for a phase row) (`rPDULoadStatusBankNumber`).
- `LoadState`: Load state (1 normal, 2 low, 3 near overload, 4 overload) (`rPDULoadStatusLoadState`).
- `CurrentA`: Current, amperes (from tenths of an ampere) (`$rPDULoadStatusLoad / 10.0`).

### APC Rack PDU - Outlets (rPDU) (1 minute)

Each outlet (rPDU branch): whether it is on, and the phase and bank feeding it. Rows are labelled by outlet name.

From `PowerNet-MIB::rPDUOutletStatusTable`, one row per `rPDUOutletStatusOutletName`.

- `Phase`: Phase or phases feeding the outlet (1 phase 1, 2 phase 2, 3 phase 3, 4 phases 1-2, 5 phases 2-3, 6 phases 3-1) (`rPDUOutletStatusOutletPhase`).
- `Bank`: Bank number (`rPDUOutletStatusOutletBank`).
- `State`: Outlet state (1 on, 2 off) (`rPDUOutletStatusOutletState`).

### APC Rack PDU - Outlet Current (rPDU) (1 minute)

Each outlet's current (rPDU branch), on outlet-metered models only: PowerNet-MIB says other models do not support the column, so it is a monitor of its own and the outlet state does not depend on it. Rows are labelled by outlet name.

From `PowerNet-MIB::rPDUOutletStatusTable`, one row per `rPDUOutletStatusOutletName`.

- `CurrentA`: Current, amperes (from tenths of an ampere) (`$rPDUOutletStatusLoad / 10.0`).

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
