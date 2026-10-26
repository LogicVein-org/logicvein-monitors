# Juniper SRX Firewall Operational and Health and Performance Collections

Last updated: 2026-10-24

Tested against: Not yet validated against a device.

These monitor sets are for: Juniper SRX firewalls, standalone or in a chassis cluster.

The folder holds two sets; assign both to the same devices:

- **Juniper SRX Firewall - Operational Collection** (3 monitors) covers what the device does (its role's state, load and services).
- **Juniper SRX Firewall - Health and Performance Collection** (2 monitors) covers the device itself (CPU, memory, storage and hardware).

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

- `BGP4-V2-MIB-JUNIPER`
- `JUNIPER-MIB`
- `JUNIPER-SRX5000-SPU-MONITORING-MIB`

ThirdEye's built-in MIB store already includes all of these modules, so no MIB import is needed. The MIB files are not included with these collections; to install or update a module, obtain the files as described below and import them with their dependencies.

### Obtaining the Juniper Networks MIBs

Download the MIB files for the device's software release from Juniper Networks' support site, and import the modules listed above together with their dependencies.

## Installation

1. In **Inventory**, use **Tools > SNMP System Info** to verify that ThirdEye can query each target device.
2. Go to **Monitors > Sets** and import `Juniper SRX Firewall - Operational Collection.3em` and `Juniper SRX Firewall - Health and Performance Collection.3em`.
3. Assign **Juniper SRX Firewall - Operational Collection** and **Juniper SRX Firewall - Health and Performance Collection** to the appropriate devices.
4. Wait at least 1 minute so every monitor has had a collection opportunity, then confirm that current data is present for the applicable monitors.

Automatic assignment to newly discovered devices is disabled in both sets. Review the sets before enabling automatic assignment in your environment.

These host queries match the devices the sets are meant for: `sysDescr=*SRX*`, `sysDescr=*srx*`.

## Included monitors

**Juniper SRX Firewall - Operational Collection** contains 3 SNMP monitors and **Juniper SRX Firewall - Health and Performance Collection** contains 2 SNMP monitors. Metrics carry friendly names; each lists the MIB object it reads or the formula it computes.

### Juniper SRX Firewall - Operational Collection

#### Juniper - BGP Peers (1 minute)

Session state of every BGP peer, with its remote AS. Rows are identified by the peer's routing instance and addresses (the table index); there is no name column.

From `BGP4-V2-MIB-JUNIPER::jnxBgpM2PeerTable`.

- `State`: BGP session state (idle, connect, active, opensent, openconfirm, established) (`jnxBgpM2PeerState`).
- `RemoteAs`: The peer's autonomous system number (`jnxBgpM2PeerRemoteAs`).

#### Juniper SRX - SPU Sessions (1 minute)

Per services processing unit (SPU) on SRX firewalls: flow sessions against the SPU's maximum.

From `JUNIPER-SRX5000-SPU-MONITORING-MIB::jnxJsSPUMonitoringObjectsTable`, one row per `jnxJsSPUMonitoringNodeDescr`.

- `FlowSessions`: Current flow sessions on the SPU (`jnxJsSPUMonitoringCurrentFlowSession`).
- `FlowSessionsPct`: Current flow sessions as a percentage of the SPU's maximum (`$FlowSessions * 100 / $jnxJsSPUMonitoringMaxFlowSession`).

#### Juniper SRX - Sessions (1 minute)

Total sessions per SRX chassis or cluster node, against the maximum, and the session creation rate.

From `JUNIPER-SRX5000-SPU-MONITORING-MIB::jnxSPUClusterObjectsTable`, one row per `jnxJsClusterMonitoringNodeDescr`.

- `Sessions`: Current total sessions on the node (`jnxJsNodeCurrentTotalSession`).
- `SessionsPct`: Current sessions as a percentage of the node's maximum (`$Sessions * 100 / $jnxJsNodeMaxTotalSession`).
- `NewSessionsPerSec`: Sessions created per second, as the device reports it (`jnxJsNodeSessionCreationPerSecond`).

### Juniper SRX Firewall - Health and Performance Collection

#### Juniper - Component Health (1 minute)

Per chassis component (routing engines, FPCs, PICs, fans, power supplies): state, temperature, CPU and memory use. Values the component does not have read 0.

From `JUNIPER-MIB::jnxOperatingTable`, one row per `jnxOperatingDescr`.
Covers the TES built-in templates *Juniper Junos - CPU Utilization*, *Juniper Junos - Memory Utilization*: it polls every object they poll, under its own names.

- `State`: Operating state (unknown, running, ready, reset, runningAtFullSpeed, down, standby) (`jnxOperatingState`).
- `TemperatureC`: Temperature, degrees Celsius (`jnxOperatingTemp`).
- `CpuPct`: CPU utilization, percent (`jnxOperatingCPU`).
- `Cpu5MinPct`: CPU utilization averaged over the last 5 minutes, percent (`jnxOperating5MinAvgCPU`).
- `BufferPct`: Buffer pool utilization, percent (`jnxOperatingBuffer`).
- `HeapPct`: Heap utilization, percent (`jnxOperatingHeap`).

#### Juniper SRX - SPU Resources (1 minute)

Per services processing unit (SPU): CPU and memory use.

From `JUNIPER-SRX5000-SPU-MONITORING-MIB::jnxJsSPUMonitoringObjectsTable`, one row per `jnxJsSPUMonitoringNodeDescr`.

- `CpuPct`: SPU CPU utilization, percent (`jnxJsSPUMonitoringCPUUsage`).
- `MemoryPct`: SPU memory usage, percent (`jnxJsSPUMonitoringMemoryUsage`).

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
