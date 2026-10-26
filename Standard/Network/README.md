# Standard Network Interfaces Health and Performance Collection

Last updated: 2026-10-24

Tested against: HP ProCurve 2626 and Cisco Catalyst 2950

This monitor set is for: Any SNMP router, switch or firewall: interface traffic, status and errors.

The collection is a starting point. It defines no alert policies or thresholds.

## Requirements

Before importing the monitor set, confirm that:

- The device has been added to the ThirdEye inventory.
- ThirdEye can reach the device's SNMP agent, normally over UDP port 161.
- A working SNMP credential has been assigned to the device in ThirdEye. SNMPv3 with authentication and privacy is recommended.
- The MIB modules listed below are loaded in ThirdEye.

### MIB modules

The collection uses these modules:

- `IF-MIB`

ThirdEye's built-in MIB store already includes this module, so no MIB import is needed. The MIB files are not included with this collection; to install or update a module, obtain the files as described below and import them with their dependencies.

### Obtaining the standard MIBs

`IF-MIB` is an IETF standard module, defined in RFC 2863. Its text is published by the [RFC Editor](https://www.rfc-editor.org/rfc/rfc2863).

## Installation

1. In **Inventory**, use **Tools > SNMP System Info** to verify that ThirdEye can query each target device.
2. Go to **Monitors > Sets** and import `Standard Network Interfaces - Health and Performance Collection.3em`.
3. Assign **Standard Network Interfaces - Health and Performance Collection** to the appropriate devices.
4. Wait at least 1 minute so every monitor has had a collection opportunity, then confirm that current data is present for the applicable monitors.

Automatic assignment to newly discovered devices is disabled in the set. Review the set before enabling automatic assignment in your environment.

These host queries match the devices the set is meant for: `traits=snmp`.

## Included monitors

The set contains 2 SNMP monitors. Metrics carry friendly names; each lists the MIB object it reads or the formula it computes.

### Interfaces - Traffic (1 minute)

Throughput, utilization and packet rates per interface, with the bytes and packets moved since the previous poll, from the 64-bit counters.

From `IF-MIB::ifXTable`, one row per `ifName`.
Covers the TES built-in template *Interface Stats (HighSpeed)*: it polls every object that template polls, under its own names.

- `BitsInPerSec`: Inbound traffic, bits per second (`rate(ifHCInOctets) * 8`).
- `BitsOutPerSec`: Outbound traffic, bits per second (`rate(ifHCOutOctets) * 8`).
- `SpeedMbps`: Interface speed, Mb/s (`ifHighSpeed`).
- `UtilizationInPct`: Inbound traffic as a percentage of the interface speed (SpeedMbps) (`rate(ifHCInOctets) * 8 / ($SpeedMbps * 10000)`).
- `UtilizationOutPct`: Outbound traffic as a percentage of the interface speed (SpeedMbps) (`rate(ifHCOutOctets) * 8 / ($SpeedMbps * 10000)`).
- `BytesIn`: Bytes received since the previous poll (`delta(ifHCInOctets)`).
- `BytesInPerSec`: Bytes received, per second (`rate(ifHCInOctets)`).
- `BytesOut`: Bytes sent since the previous poll (`delta(ifHCOutOctets)`).
- `BytesOutPerSec`: Bytes sent, per second (`rate(ifHCOutOctets)`).
- `PacketsInPerSec`: Inbound unicast, multicast and broadcast packets per second (`rate(ifHCInUcastPkts) + rate(ifHCInMulticastPkts) + rate(ifHCInBroadcastPkts)`).
- `PacketsOutPerSec`: Outbound unicast, multicast and broadcast packets per second (`rate(ifHCOutUcastPkts) + rate(ifHCOutMulticastPkts) + rate(ifHCOutBroadcastPkts)`).
- `PacketsIn`: Unicast, multicast and broadcast packets received since the previous poll (`delta(ifHCInUcastPkts) + delta(ifHCInMulticastPkts) + delta(ifHCInBroadcastPkts)`).
- `PacketsOut`: Unicast, multicast and broadcast packets sent since the previous poll (`delta(ifHCOutUcastPkts) + delta(ifHCOutMulticastPkts) + delta(ifHCOutBroadcastPkts)`).

### Interfaces - Status and Errors (1 minute)

Each interface's alias and its administrative and operational state, and errored and discarded packets, as rates and as counts since the previous poll. The rows are labelled by ifName, and the alias is read from ifXTable, matched to the ifTable rows by ifIndex.

From `IF-MIB::ifTable`, one row per `ifName`.

- `Alias`: Interface alias, the description an administrator gave the interface (`ifAlias`).
- `AdminStatus`: Administrative state (up, down, testing) (`ifAdminStatus`).
- `OperStatus`: Operational state (up, down, testing, unknown, dormant, notPresent, lowerLayerDown) (`ifOperStatus`).
- `ErrorsInPerSec`: Inbound packets with errors, per second (`rate(ifInErrors)`).
- `ErrorsIn`: Inbound packets with errors since the previous poll (`delta(ifInErrors)`).
- `ErrorsOutPerSec`: Outbound packets that could not be sent because of errors, per second (`rate(ifOutErrors)`).
- `ErrorsOut`: Outbound packets that could not be sent because of errors since the previous poll (`delta(ifOutErrors)`).
- `DiscardsInPerSec`: Inbound packets discarded without an error (for example, buffer shortage), per second (`rate(ifInDiscards)`).
- `DiscardsIn`: Inbound packets discarded without an error (for example, buffer shortage) since the previous poll (`delta(ifInDiscards)`).
- `DiscardsOutPerSec`: Outbound packets discarded without an error (for example, queue drops), per second (`rate(ifOutDiscards)`).
- `DiscardsOut`: Outbound packets discarded without an error (for example, queue drops) since the previous poll (`delta(ifOutDiscards)`).

`delta(x)` is the change in counter `x` between the last two polls; `rate(x)` is that change divided by the monitor's polling period in seconds (TES's `$pollingSecs`), a per-second rate.

## Expected variations

A table monitor returns no rows when the device lacks the feature or hardware it covers. An empty table does not necessarily indicate a collection failure: first confirm that the feature is present and configured on the device.

- Interfaces - Traffic reads the 64-bit interface counters. A device without them, such as a Cisco 2501, returns nothing for it.
- Interfaces - Status and Errors reads its status and error counters from ifTable, and each interface's name (ifName) and Alias from ifXTable, matched by ifIndex. A device that does not implement ifXTable returns no ifName or ifAlias.

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
