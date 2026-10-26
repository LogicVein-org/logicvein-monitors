# Yamaha RT Router Operational and Health and Performance Collections

Last updated: 2026-10-24

Tested against: Not yet validated against a device.

These monitor sets are for: Yamaha RT series routers.

The folder holds two sets; assign both to the same devices:

- **Yamaha RT Router - Operational Collection** (6 monitors) covers what the device does (its role's state, load and services).
- **Yamaha RT Router - Health and Performance Collection** (4 monitors) covers the device itself (CPU, memory, storage and hardware).

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

- `YAMAHA-RT-HARDWARE`
- `YAMAHA-RT-INTERFACES`
- `YAMAHA-RT-IP`

ThirdEye's built-in MIB store already includes all of these modules, so no MIB import is needed. The MIB files are not included with these collections; to install or update a module, obtain the files as described below and import them with their dependencies.

### Obtaining the Yamaha MIBs

Download the MIB files for the device's software release from Yamaha's network products website, and import the modules listed above together with their dependencies.

## Installation

1. In **Inventory**, use **Tools > SNMP System Info** to verify that ThirdEye can query each target device.
2. Go to **Monitors > Sets** and import `Yamaha RT Router - Operational Collection.3em` and `Yamaha RT Router - Health and Performance Collection.3em`.
3. Assign **Yamaha RT Router - Operational Collection** and **Yamaha RT Router - Health and Performance Collection** to the appropriate devices.
4. Wait at least 1 minute so every monitor has had a collection opportunity, then confirm that current data is present for the applicable monitors.

Automatic assignment to newly discovered devices is disabled in both sets. Review the sets before enabling automatic assignment in your environment.

These host queries match the devices the sets are meant for: `sysObjectID=1.3.6.1.4.1.1182.*`.

## Included monitors

**Yamaha RT Router - Operational Collection** contains 6 SNMP monitors and **Yamaha RT Router - Health and Performance Collection** contains 4 SNMP monitors. Metrics carry friendly names; each lists the MIB object it reads or the formula it computes.

### Yamaha RT Router - Operational Collection

#### Yamaha RT - Tunnels (1 minute)

Each tunnel interface: whether its link is up. Rows are identified by the router's interface index.

From `YAMAHA-RT-INTERFACES::yrIfTunnelTable`.

- `Link`: Tunnel link (1 up, 2 down) (`yrIfTunnelLink`).

#### Yamaha RT - IP Keepalive (1 minute)

Each IP keepalive configuration: its type and status. Rows are identified by keepalive configuration.

From `YAMAHA-RT-IP::yrIpKeepaliveTable`.

- `Type`: Keepalive type (1 icmp-echo) (`yrIpKeepaliveType`).
- `Status`: Keepalive status (1 up, 2 down, 3 up-wait, 4 down-wait) (`yrIpKeepaliveStatus`).

#### Yamaha RT - PP Interfaces (1 minute)

Each PP interface: its admin and operational status, input and output utilization, and bytes and errors in and out, as rates and as counts since the previous poll, from the 64-bit counters. Rows are identified by interface index, labelled by description.

From `YAMAHA-RT-INTERFACES::yrIfPpTable`, one row per `yrIfPpDescr`.

- `AdminStatus`: Admin status (1 up, 2 down, 3 testing) (`yrIfPpAdminStatus`).
- `OperStatus`: Operational status (1 up, 2 down, 3 testing) (`yrIfPpOperStatus`).
- `UtilizationInPct`: Input utilization, percent (`yrIfPpInUtil`).
- `UtilizationOutPct`: Output utilization, percent (`yrIfPpOutUtil`).
- `BytesIn`: Bytes received since the previous poll (`delta(yrIfPpHCInOctets)`).
- `BytesInPerSec`: Bytes received, per second (`rate(yrIfPpHCInOctets)`).
- `BytesOut`: Bytes sent since the previous poll (`delta(yrIfPpHCOutOctets)`).
- `BytesOutPerSec`: Bytes sent, per second (`rate(yrIfPpHCOutOctets)`).
- `ErrorsIn`: Inbound errors since the previous poll (`delta(yrIfPpHCInErrors)`).
- `ErrorsInPerSec`: Inbound errors, per second (`rate(yrIfPpHCInErrors)`).
- `ErrorsOut`: Outbound errors since the previous poll (`delta(yrIfPpHCOutErrors)`).
- `ErrorsOutPerSec`: Outbound errors, per second (`rate(yrIfPpHCOutErrors)`).

#### Yamaha RT - WWAN (1 minute)

Each wireless WAN (WWAN) interface: its status, and packets sent and received, as rates and as counts since the previous poll. Rows are identified by WWAN interface.

From `YAMAHA-RT-INTERFACES::yrIfWwanTable`.

- `Status`: Status (1 up, 2 down, 3 calling) (`yrIfWwanStatus`).
- `PacketsSent`: Packets sent since the previous poll (`delta(yrIfWwanTransmitted)`).
- `PacketsSentPerSec`: Packets sent, per second (`rate(yrIfWwanTransmitted)`).
- `PacketsReceived`: Packets received since the previous poll (`delta(yrIfWwanReceived)`).
- `PacketsReceivedPerSec`: Packets received, per second (`rate(yrIfWwanReceived)`).

#### Yamaha RT - Mobile Signal (1 minute)

Each mobile (cellular) interface: the device's state and its latest signal strength, both as the router reports them as text. Rows are identified by mobile interface. The MIB's antenna level, dBm and signal quality objects are not-accessible (trap varbinds only), so they are not polled.

From `YAMAHA-RT-INTERFACES::yrIfMobileTable`.

- `Status`: The device's state, as text (`yrIfMobileStatus`).
- `SignalStrength`: The latest signal strength the router acquired, as text (`yrIfMobileSignalStrength`).

#### Yamaha RT - WWAN Modules (1 minute)

Each wireless WAN (WWAN) module: whether it is powered, whether its network is ready, the radio technology in use, and its antenna level. Rows are identified by the router's interface index.

From `YAMAHA-RT-INTERFACES::yrIfWwanModTable`.

- `Power`: Module power (1 on, 2 off) (`yrIfWwanModPower`).
- `Network`: Network status (1 ready, 2 notReady) (`yrIfWwanModNwStat`).
- `RadioTechnology`: Radio access technology (1 unknown, 2 fourthGeneration-LTE, 3 thirdGeneration-3G) (`yrIfWwanModRAT`).
- `AntennaLevel`: Antenna level (1 unknown, 2 noService, 3 level0, 4 level1, 5 level2, 6 level3) (`yrIfWwanModAntLevel`).

### Yamaha RT Router - Health and Performance Collection

#### Yamaha RT - CPU and Memory (1 minute)

CPU utilization over 5 seconds, 1 minute and 5 minutes, and memory use.

From `YAMAHA-RT-HARDWARE` scalars.
Covers the TES built-in templates *YAMAHA - CPUUseRate (5min)*, *YAMAHA - Memory usage*: it polls every object they poll, under its own names.

- `Cpu5SecPct`: CPU utilization over 5 seconds, percent (`yrhCpuUtil5sec`).
- `Cpu1MinPct`: CPU utilization over 1 minute, percent (`yrhCpuUtil1min`).
- `Cpu5MinPct`: CPU utilization over 5 minutes, percent (`yrhCpuUtil5min`).
- `MemoryPct`: Main memory utilization, percent (`yrhMemoryUtil`).
- `MemoryBytes`: Size of main memory, bytes (`yrhMemorySize`).

#### Yamaha RT - Fans (1 minute)

Status of each fan module.

From `YAMAHA-RT-HARDWARE::yrhFanModuleTable`.
Covers the TES built-in template *YAMAHA - FanStatus*: it polls every object that template polls, under its own names.

- `Status`: Fan module status (1 good, 2 no good) (`yrhFanModuleStatus`).

#### Yamaha RT - Power Supplies (1 minute)

Status of each power module.

From `YAMAHA-RT-HARDWARE::yrhPowerModuleTable`.
Covers the TES built-in template *YAMAHA - Power status*: it polls every object that template polls, under its own names.

- `Status`: Power module status (1 up, 2 down, 3 not installed) (`yrhPowerModuleStatus`).

#### Yamaha RT - Temperature (1 minute)

Temperature inside the chassis.

From `YAMAHA-RT-HARDWARE` scalars.
Covers the TES built-in template *YAMAHA - Temperature*: it polls every object that template polls, under its own names.

- `Temperature`: Temperature inside the chassis, as the router reports it (unit not confirmed from a MIB source) (`yrhInboxTemperature`).

`delta(x)` is the change in counter `x` between the last two polls; `rate(x)` is that change divided by the monitor's polling period in seconds (TES's `$pollingSecs`), a per-second rate.

## Expected variations

A table monitor returns no rows when the device lacks the feature or hardware it covers. An empty table does not necessarily indicate a collection failure: first confirm that the feature is present and configured on the device.

- Each monitor returns rows only where the router has that kind of interface or configuration: tunnels, IP keepalive, PP connections, a WWAN interface or module, or a mobile device.

## Validation

To run the **SNMP System Info** tool, open **Inventory**, select the target device, open **Tools**, and select **SNMP System Info**. A successful test returns basic system information, including the system description and uptime, rather than an SNMP timeout or authentication error.

After installation:

1. Run **SNMP System Info** for every assigned device and confirm that it succeeds.
2. Confirm that the one-minute monitors have current samples.
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
