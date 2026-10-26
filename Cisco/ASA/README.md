# Cisco ASA Firewall Operational and Health and Performance Collections

Last updated: 2026-10-24

Tested against: Cisco ASA 5505 running software 8.2

These monitor sets are for: Cisco ASA and Firepower appliances running ASA software.

The folder holds two sets; assign both to the same devices:

- **Cisco ASA Firewall - Operational Collection** (5 monitors) covers what the device does (its role's state, load and services).
- **Cisco ASA Firewall - Health and Performance Collection** (2 monitors) covers the device itself (CPU, memory, storage and hardware).

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

- `CISCO-FIREWALL-MIB`
- `CISCO-IPSEC-FLOW-MONITOR-MIB`
- `CISCO-MEMORY-POOL-MIB`
- `CISCO-PROCESS-MIB`
- `CISCO-REMOTE-ACCESS-MONITOR-MIB`

ThirdEye's built-in MIB store already includes all of these modules, so no MIB import is needed. The MIB files are not included with these collections; to install or update a module, obtain the files as described below and import them with their dependencies.

### Obtaining the Cisco MIBs

Download the MIB files from the [Cisco MIB download site](https://cfnng.cisco.com/mibs) or Cisco's official [MIB repository](https://github.com/cisco/cisco-mibs/tree/main/v2), and import the modules listed above together with their dependencies.

## Installation

1. In **Inventory**, use **Tools > SNMP System Info** to verify that ThirdEye can query each target device.
2. Go to **Monitors > Sets** and import `Cisco ASA Firewall - Operational Collection.3em` and `Cisco ASA Firewall - Health and Performance Collection.3em`.
3. Assign **Cisco ASA Firewall - Operational Collection** and **Cisco ASA Firewall - Health and Performance Collection** to the appropriate devices.
4. Wait at least 1 minute so every monitor has had a collection opportunity, then confirm that current data is present for the applicable monitors.

Automatic assignment to newly discovered devices is disabled in both sets. Review the sets before enabling automatic assignment in your environment.

These host queries match the devices the sets are meant for: `sysDescr=*Adaptive Security Appliance*`.

## Included monitors

**Cisco ASA Firewall - Operational Collection** contains 5 SNMP monitors and **Cisco ASA Firewall - Health and Performance Collection** contains 2 SNMP monitors. Metrics carry friendly names; each lists the MIB object it reads or the formula it computes.

### Cisco ASA Firewall - Operational Collection

#### Cisco ASA - Connections (1 minute)

Firewall connection counts: in use now and the highest since boot.

From `CISCO-FIREWALL-MIB::cfwConnectionStatTable`, one row per `cfwConnectionStatDescription`.

- `Connections`: The row's connection count (rows: currently in use, most used since boot) (`cfwConnectionStatValue`).

#### Cisco ASA - Failover Status (1 minute)

Which unit is active and whether failover is healthy, per hardware component (primary unit, secondary unit, failover LAN interface).

From `CISCO-FIREWALL-MIB::cfwHardwareStatusTable`, one row per `cfwHardwareInformation`.

- `Status`: Component status (other, up, down, error, overTemp, busy, noMedia, backup, active, standby) (`cfwHardwareStatusValue`).

#### Cisco ASA - Remote Access Sessions (1 minute)

Remote-access VPN load: active sessions and users, and how fast sessions end.

From `CISCO-REMOTE-ACCESS-MONITOR-MIB` scalars.
Covers the TES built-in template *Cisco ASA - VPNSessions*: it polls every object that template polls, under its own names.

- `ActiveSessions`: Remote-access sessions now active (`crasNumSessions`).
- `ActiveUsers`: Distinct users with an active session (`crasNumUsers`).
- `EndedSessionsPerSec`: Sessions that ended, per second (`rate(crasNumPrevSessions)`).
- `EndedSessions`: Sessions that ended since the previous poll (`delta(crasNumPrevSessions)`).

#### Cisco - IPsec Tunnels (1 minute)

Active IKE and IPsec tunnels and encrypted traffic, for routers and ASAs that terminate VPNs.

From `CISCO-IPSEC-FLOW-MONITOR-MIB` scalars.

- `IkeTunnels`: Active IKE (phase 1) tunnels (`cikeGlobalActiveTunnels`).
- `IpsecTunnels`: Active IPsec (phase 2) tunnels (`cipSecGlobalActiveTunnels`).
- `BitsInPerSec`: Traffic decrypted by all IPsec tunnels, bits per second (`rate(cipSecGlobalHcInOctets) * 8`).
- `BitsOutPerSec`: Traffic encrypted by all IPsec tunnels, bits per second (`rate(cipSecGlobalHcOutOctets) * 8`).
- `BytesIn`: Bytes decrypted by all IPsec tunnels since the previous poll (`delta(cipSecGlobalHcInOctets)`).
- `BytesInPerSec`: Bytes decrypted by all IPsec tunnels, per second (`rate(cipSecGlobalHcInOctets)`).
- `BytesOut`: Bytes encrypted by all IPsec tunnels since the previous poll (`delta(cipSecGlobalHcOutOctets)`).
- `BytesOutPerSec`: Bytes encrypted by all IPsec tunnels, per second (`rate(cipSecGlobalHcOutOctets)`).

#### Cisco - IPsec Errors (1 minute)

IPsec packets dropped in each direction and IKE tunnel setup failures, as rates and as counts since the previous poll.

From `CISCO-IPSEC-FLOW-MONITOR-MIB` scalars.

- `DropsInPerSec`: Inbound IPsec packets dropped, per second (`rate(cipSecGlobalInDrops)`).
- `DropsIn`: Inbound IPsec packets dropped since the previous poll (`delta(cipSecGlobalInDrops)`).
- `DropsOutPerSec`: Outbound IPsec packets dropped, per second (`rate(cipSecGlobalOutDrops)`).
- `DropsOut`: Outbound IPsec packets dropped since the previous poll (`delta(cipSecGlobalOutDrops)`).
- `TunnelFailuresPerSec`: IKE tunnels this device failed to set up, as initiator or responder, per second (`rate(cikeGlobalInitTunnelFails) + rate(cikeGlobalRespTunnelFails)`).
- `TunnelFailures`: IKE tunnels this device failed to set up, as initiator or responder since the previous poll (`delta(cikeGlobalInitTunnelFails) + delta(cikeGlobalRespTunnelFails)`).

### Cisco ASA Firewall - Health and Performance Collection

#### Cisco ASA - CPU Utilization (1 minute)

CPU busy percentage over 5 seconds, 1 minute and 5 minutes, from the original CISCO-PROCESS-MIB objects that ASA software answers (an ASA 5505 on 8.2 returns nothing for the newer Rev objects).

From `CISCO-PROCESS-MIB::cpmCPUTotalTable`.
Covers the TES built-in templates *Cisco CPU*, *Cisco Catalyst 9800 WLC - CPU Utilization [1min Avg]*, *Cisco Catalyst 9800 WLC - CPU Utilization [5min Avg]*: it polls every object they poll, under its own names.

- `Busy5SecPct`: CPU busy over the last 5 seconds, percent (`cpmCPUTotal5sec`).
- `Busy1MinPct`: CPU busy over the last minute, percent (`cpmCPUTotal1min`).
- `Busy5MinPct`: CPU busy over the last 5 minutes, percent (`cpmCPUTotal5min`).

#### Cisco - Memory Pools (1 minute)

Used and free memory per pool (processor, I/O) on classic IOS, and the largest free block, which shows fragmentation.

From `CISCO-MEMORY-POOL-MIB::ciscoMemoryPoolTable`, one row per `ciscoMemoryPoolName`.

- `UsedBytes`: Bytes in use (`ciscoMemoryPoolUsed`).
- `FreeBytes`: Bytes free (`ciscoMemoryPoolFree`).
- `LargestFreeBytes`: Largest contiguous free block, bytes (`ciscoMemoryPoolLargestFree`).
- `UsedPct`: Used as a percentage of used plus free (`$UsedBytes * 100 / ($UsedBytes + $FreeBytes)`).

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
