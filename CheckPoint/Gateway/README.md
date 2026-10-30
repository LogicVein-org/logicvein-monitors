# Check Point Gateway Operational and Health and Performance Collections

Last updated: 2026-10-30

Tested against: Not yet validated against a device.

These monitor sets are for: Check Point security gateways.

The folder holds two sets; assign both to the same devices:

- **Check Point Gateway - Operational Collection** (1 monitor) covers what the device does (its role's state, load and services).
- **Check Point Gateway - Health and Performance Collection** (2 monitors) covers the device itself (CPU, memory, storage and hardware).

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

- `CHECKPOINT-MIB`

ThirdEye's built-in MIB store already includes this module, so no MIB import is needed. The MIB files are not included with these collections; to install or update a module, obtain the files as described below and import them with their dependencies.

### Obtaining the Check Point MIBs

Download the MIB files for the device's software release from Check Point's Support Center, and import the modules listed above together with their dependencies.

## Installation

1. In **Inventory**, use **Tools > SNMP System Info** to verify that ThirdEye can query each target device.
2. Go to **Monitors > Sets** and import `Check Point Gateway - Operational Collection.3em` and `Check Point Gateway - Health and Performance Collection.3em`.
3. Assign **Check Point Gateway - Operational Collection** and **Check Point Gateway - Health and Performance Collection** to the appropriate devices.
4. Wait at least 5 minutes so every monitor has had a collection opportunity, then confirm that current data is present for the applicable monitors.

Automatic assignment to newly discovered devices is disabled in both sets. Review the sets before enabling automatic assignment in your environment.

These host queries match the devices the sets are meant for: `sysObjectID=1.3.6.1.4.1.2620.*`.

## Included monitors

**Check Point Gateway - Operational Collection** contains 1 SNMP monitor and **Check Point Gateway - Health and Performance Collection** contains 2 SNMP monitors. Metrics carry friendly names; each lists the MIB object it reads or the formula it computes.

### Check Point Gateway - Operational Collection

#### Check Point - Firewall (5 minutes)

Firewall connection table load, and packets accepted, rejected and dropped, as rates and as counts since the previous poll.

From `CHECKPOINT-MIB` scalars.

- `Connections`: Connections in the firewall connection table (`fwNumConn`).
- `PeakConnections`: Peak number of connections (`fwPeakNumConn`).
- `AcceptedPerSec`: Packets accepted, per second (`rate(fwAccepted)`).
- `RejectedPerSec`: Packets rejected, per second (`rate(fwRejected)`).
- `DroppedPerSec`: Packets dropped, per second (`rate(fwDropped)`).
- `Accepted`: Packets accepted since the previous poll (`delta(fwAccepted)`).
- `Rejected`: Packets rejected since the previous poll (`delta(fwRejected)`).
- `Dropped`: Packets dropped since the previous poll (`delta(fwDropped)`).

### Check Point Gateway - Health and Performance Collection

#### Check Point - CPU (5 minutes)

Overall processor usage of the Check Point host (SVN OS objects).

From `CHECKPOINT-MIB` scalars.

- `CpuPct`: Processor usage, percent (`procUsage`).

#### Check Point - CPU per Core (5 minutes)

Usage of each processor core.

From `CHECKPOINT-MIB::multiProcTable`.

- `CpuPct`: Processor core usage, percent (`multiProcUsage`).

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
