# Standard Server Health and Performance Collection

Last updated: 2026-10-24

Tested against: Not yet validated against a device.

This monitor set is for: Servers answering the HOST-RESOURCES-MIB (Net-SNMP and Windows agents; VMware ESXi unverified).

The collection is a starting point. It defines no alert policies or thresholds.

## Requirements

Before importing the monitor set, confirm that:

- The device has been added to the ThirdEye inventory.
- ThirdEye can reach the device's SNMP agent, normally over UDP port 161.
- A working SNMP credential has been assigned to the device in ThirdEye. SNMPv3 with authentication and privacy is recommended.
- The MIB modules listed below are loaded in ThirdEye.

### MIB modules

The collection uses these modules:

- `HOST-RESOURCES-MIB`

ThirdEye's built-in MIB store already includes this module, so no MIB import is needed. The MIB files are not included with this collection; to install or update a module, obtain the files as described below and import them with their dependencies.

### Obtaining the standard MIBs

`HOST-RESOURCES-MIB` is an IETF standard module, defined in RFC 2790. Its text is published by the [RFC Editor](https://www.rfc-editor.org/rfc/rfc2790).

## Installation

1. In **Inventory**, use **Tools > SNMP System Info** to verify that ThirdEye can query each target device.
2. Go to **Monitors > Sets** and import `Standard Server - Health and Performance Collection.3em`.
3. Assign **Standard Server - Health and Performance Collection** to the appropriate devices.
4. Wait at least 1 minute so every monitor has had a collection opportunity, then confirm that current data is present for the applicable monitors.

Automatic assignment to newly discovered devices is disabled in the set. Review the set before enabling automatic assignment in your environment.

These host queries match the devices the set is meant for: `sysObjectID=1.3.6.1.4.1.8072.*`, `sysObjectID=1.3.6.1.4.1.311.*`, `sysObjectID=1.3.6.1.4.1.6876.*`.

## Included monitors

The set contains 3 SNMP monitors. Metrics carry friendly names; each lists the MIB object it reads or the formula it computes.

### Server - Storage (1 minute)

Use of each storage area (file systems, physical and virtual memory), as the host reports them.

From `HOST-RESOURCES-MIB::hrStorageTable`, one row per `hrStorageDescr`.
Covers the TES built-in template *Storage Stats*: it polls every object that template polls, under its own names.

- `AllocationUnit`: Size of one allocation unit, bytes (`hrStorageAllocationUnits`).
- `Size`: Size, in allocation units (`hrStorageSize`).
- `Used`: Space allocated, in allocation units (`hrStorageUsed`).
- `SizeBytes`: Size, bytes (`$Size * $AllocationUnit`).
- `UsedBytes`: Space allocated, bytes (`$Used * $AllocationUnit`).
- `FreeBytes`: Size less space allocated, bytes (`($Size - $Used) * $AllocationUnit`).
- `UsedPct`: Space allocated as a percentage of the size (`$Used * 100.0 / $Size`).
- `AllocationFailures`: Allocation requests refused for lack of space since the previous poll (`delta(hrStorageAllocationFailures)`).
- `AllocationFailuresPerSec`: Allocation requests refused for lack of space, per second (`rate(hrStorageAllocationFailures)`).

### Server - CPU (1 minute)

Load of each processor, averaged over the last minute.

From `HOST-RESOURCES-MIB::hrProcessorTable`, one row per `hrDeviceDescr`.
Covers the TES built-in template *CPU Stats*: it polls every object that template polls, under its own names.

- `LoadPct`: Percentage of time the processor was not idle over the last minute (`hrProcessorLoad`).

### Server - Processes and Users (1 minute)

Processes loaded or running, and user sessions.

From `HOST-RESOURCES-MIB` scalars.

- `Processes`: Process contexts loaded or running (`hrSystemProcesses`).
- `Users`: User sessions the host holds state for (`hrSystemNumUsers`).

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
