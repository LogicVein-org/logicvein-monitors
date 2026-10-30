# Palo Alto Operational and Health and Performance Collections

Last updated: 2026-10-30

Tested against: Palo Alto Networks VM-Series firewall with PAN-OS 11.2 MIBs

These monitor sets are for: Palo Alto Networks PA-series and VM-series firewalls.

The folder holds two sets; assign both to the same devices:

- **Palo Alto - Operational Collection** (16 monitors) covers what the device does (its role's state, load and services).
- **Palo Alto - Health and Performance Collection** (2 monitors) covers the device itself (CPU, memory, storage and hardware).

The collections are a starting point. They define no alert policies or thresholds.

Interface traffic and errors are not included: assign **Standard Network Interfaces - Health and Performance Collection** to the same devices for those.

## Requirements

Before importing the monitor sets, confirm that:

- The device has been added to the ThirdEye inventory.
- ThirdEye can reach the device's SNMP agent, normally over UDP port 161.
- A working SNMP credential has been assigned to the device in ThirdEye. SNMPv3 with authentication and privacy is recommended.
- SNMP has been configured on the firewall and the connection path permits queries from the ThirdEye server.
- The MIB modules listed below are loaded in ThirdEye.

### MIB modules

The collections use these modules:

- `HOST-RESOURCES-MIB`
- `PAN-COMMON-MIB`

ThirdEye's built-in MIB store already includes all of these modules. Palo Alto - Session Setup Rate, Palo Alto - VSYS Session Setup Rates, Palo Alto - Admin Sessions, Palo Alto - Security Content Release Dates, Palo Alto - Log Delivery Health, Palo Alto - External Log Forwarding Queues, Palo Alto - Log Collector Connections and Palo Alto - Log Storage and Retention need the vendor's current file of `PAN-COMMON-MIB` imported with its dependencies: the objects they poll are not in ThirdEye's built-in copies. Without that import those monitors collect nothing; the other monitors are unaffected. The MIB files are not included with these collections; to install or update a module, obtain the files as described below and import them with their dependencies.

### Obtaining the Palo Alto Networks MIBs

Download the enterprise MIB package for the PAN-OS version being monitored from Palo Alto Networks' Technical Documentation portal:

- [Enterprise SNMP MIB files](https://docs.paloaltonetworks.com/resources/snmp-mib-files)

Import the complete package so that the dependencies and textual conventions used by `PAN-COMMON-MIB` can be resolved.

## Installation

1. Import the MIB modules listed under Requirements, for the monitors that need them.
2. In **Inventory**, use **Tools > SNMP System Info** to verify that ThirdEye can query each target device.
3. Go to **Monitors > Sets** and import `Palo Alto - Operational Collection.3em` and `Palo Alto - Health and Performance Collection.3em`.
4. Assign **Palo Alto - Operational Collection** and **Palo Alto - Health and Performance Collection** to the appropriate devices.
5. Wait at least 15 minutes so every monitor has had a collection opportunity, then confirm that current data is present for the applicable monitors.

Automatic assignment to newly discovered devices is disabled in both sets. Review the sets before enabling automatic assignment in your environment.

These host queries match the devices the sets are meant for: `sysObjectID=1.3.6.1.4.1.25461.*`.

## Included monitors

**Palo Alto - Operational Collection** contains 16 SNMP monitors and **Palo Alto - Health and Performance Collection** contains 2 SNMP monitors. Metrics carry friendly names; each lists the MIB object it reads or the formula it computes.

### Palo Alto - Operational Collection

#### Palo Alto - Sessions (5 minutes)

Session table load: utilization and capacity, active sessions overall and by TCP, UDP and ICMP, and SSL proxy (decryption) sessions and their utilization.

From `PAN-COMMON-MIB` scalars.
Covers the TES built-in templates *Palo Alto - Active Session Count*, *Palo Alto - Session Utilization*: it polls every object they poll, under its own names.

- `UtilizationPct`: Session table utilization, percent (`panSessionUtilization`).
- `MaxSessions`: Sessions the session table can hold (`panSessionMax`).
- `Active`: Active sessions (`panSessionActive`).
- `ActiveTcp`: Active TCP sessions (`panSessionActiveTcp`).
- `ActiveUdp`: Active UDP sessions (`panSessionActiveUdp`).
- `ActiveIcmp`: Active ICMP sessions (`panSessionActiveICMP`).
- `ActiveSslProxy`: Active SSL proxy sessions (`panSessionActiveSslProxy`).
- `SslProxyUtilizationPct`: SSL proxy session utilization, percent (`panSessionSslProxyUtilization`).

#### Palo Alto - Session Setup Rate (5 minutes)

New sessions per second, as the firewall computes it (panSessionCps), which the published module polls in its Session Demand and Capacity monitor beside the objects in Palo Alto - Sessions.

From `PAN-COMMON-MIB` scalars.
Needs `PAN-COMMON-MIB` imported: the objects this monitor polls are not in ThirdEye's built-in MIB store (whose copy of `PAN-COMMON-MIB` does not define them), and their OIDs and types are those of the published logicvein-monitors module it ports.

- `SetupsPerSec`: New sessions per second, as the firewall reports it (`panSessionCps`).

#### Palo Alto - VSYS Sessions (5 minutes)

Active sessions per virtual system, and utilization where a session limit is configured.

From `PAN-COMMON-MIB::panVsysTable`, one row per `panVsysName`.
Covers the TES built-in template *Palo Alto - VSYS Session Utilization*: it polls every object that template polls, under its own names.

- `Active`: Active sessions on the virtual system (`panVsysActiveSessions`).
- `UtilizationPct`: Session utilization, percent; 0 when no session limit is configured (`panVsysSessionUtilizationPct`).
- `MaxSessions`: Session limit of the virtual system (0 when no limit is configured) (`panVsysMaxSessions`).
- `VsysId`: Virtual system ID (the row index, as a value) (`panVsysId`).

#### Palo Alto - VSYS Session Setup Rates (5 minutes)

New TCP, UDP and other IP sessions per second on each virtual system, as the firewall computes them, which the published module polls beside the columns in Palo Alto - VSYS Sessions.

From `PAN-COMMON-MIB::panVsysTable`, one row per `panVsysName`.
Needs `PAN-COMMON-MIB` imported: the objects this monitor polls are not in ThirdEye's built-in MIB store (whose copy of `PAN-COMMON-MIB` does not define them), and their OIDs and types are those of the published logicvein-monitors module it ports.

- `TcpSetupsPerSec`: New TCP sessions per second, as the firewall reports it (`panVsysActiveTcpCps`).
- `UdpSetupsPerSec`: New UDP sessions per second, as the firewall reports it (`panVsysActiveUdpCps`).
- `OtherIpSetupsPerSec`: New sessions of other IP protocols per second, as the firewall reports it (`panVsysActiveOtherIpCps`).

#### Palo Alto - Session Drops (5 minutes)

Why sessions and packets are refused: policy denies, scan detection, DoS protection, NAT and packet buffer exhaustion, TCP reassembly.

From `PAN-COMMON-MIB` scalars.

- `PolicyDeniesPerSec`: Session setups denied by security policy, per second (`rate(panFlowPolicyDeny)`).
- `PolicyDenies`: Session setups denied by security policy since the previous poll (`delta(panFlowPolicyDeny)`).
- `ScanDropsPerSec`: Session setups denied by scan detection, per second (`rate(panFlowScanDrop)`).
- `ScanDrops`: Session setups denied by scan detection since the previous poll (`delta(panFlowScanDrop)`).
- `DosDropsPerSec`: Packets dropped by DoS protection rate limiting or IP blocking, per second (`rate(panFlowDosRuleDrop)`).
- `DosDrops`: Packets dropped by DoS protection rate limiting or IP blocking since the previous poll (`delta(panFlowDosRuleDrop)`).
- `NatErrorsPerSec`: Session setups failing source NAT IP or port allocation, per second (`rate(panFlowPolicyNat)`).
- `NatErrors`: Session setups failing source NAT IP or port allocation since the previous poll (`delta(panFlowPolicyNat)`).
- `PacketAllocFailuresPerSec`: Packet buffer allocation errors, per second (`rate(panPktAllocFailure)`).
- `PacketAllocFailures`: Packet buffer allocation errors since the previous poll (`delta(panPktAllocFailure)`).
- `TcpReassemblyDropsPerSec`: Packets dropped because TCP reassembly failed, per second (`rate(panTcpDropPacket)`).
- `TcpReassemblyDrops`: Packets dropped because TCP reassembly failed since the previous poll (`delta(panTcpDropPacket)`).

#### Palo Alto - High Availability (5 minutes)

High-availability mode, this firewall's state and its peer's state.

From `PAN-COMMON-MIB` scalars.

- `Mode`: HA mode (disabled, active-passive or active-active) (`panSysHAMode`).
- `State`: This firewall's HA state, as text (`panSysHAState`).
- `PeerState`: The peer's HA state, as text (`panSysHAPeerState`).

#### Palo Alto - Panorama Connection (5 minutes)

Whether the firewall is connected to its Panorama servers.

From `PAN-COMMON-MIB` scalars.

- `Panorama1`: Connection to the first Panorama server (connected, not-connected) (`panMgmtPanoramaConnected`).
- `Panorama2`: Connection to the second Panorama server (connected, not-connected) (`panMgmtPanorama2Connected`).

#### Palo Alto - Admin Sessions (5 minutes)

Administrator sessions open on the firewall, which the published module polls beside the Panorama connection state in Palo Alto - Panorama Connection.

From `PAN-COMMON-MIB` scalars.
Needs `PAN-COMMON-MIB` imported: the objects this monitor polls are not in ThirdEye's built-in MIB store (whose copy of `PAN-COMMON-MIB` does not define them), and their OIDs and types are those of the published logicvein-monitors module it ports.

- `AdminSessions`: Administrator sessions open (`panMgmtNumAdminSessions`).

#### Palo Alto - GlobalProtect Gateway (5 minutes)

GlobalProtect gateway load: active tunnels against the maximum.

From `PAN-COMMON-MIB` scalars.

- `UtilizationPct`: Gateway utilization, percent (`panGPGWUtilizationPct`).
- `ActiveTunnels`: Active tunnels (`panGPGWUtilizationActiveTunnels`).
- `MaxTunnels`: Tunnels allowed (`panGPGWUtilizationMaxTunnels`).

#### Palo Alto - PAN-OS Software Version (15 minutes)

The PAN-OS software version, kept apart from the content versions as the published logicvein-monitors module keeps it. It changes rarely, so the monitor polls every 900 s.

From `PAN-COMMON-MIB` scalars.

- `PanOsVersion`: Full PAN-OS software version (`panSysSwVersion`).

#### Palo Alto - Security Content Versions (15 minutes)

The installed content versions (applications, threats, antivirus, WildFire, URL filtering, GlobalProtect client, OPSWAT), to spot firewalls behind on updates. These change rarely, so the monitor polls every 900 s.

From `PAN-COMMON-MIB` scalars.

- `AppVersion`: Installed application definition version (0 when none) (`panSysAppVersion`).
- `ThreatVersion`: Installed threat definition version (0 when none) (`panSysThreatVersion`).
- `AntivirusVersion`: Installed antivirus version (0 when none) (`panSysAvVersion`).
- `WildfireVersion`: Installed WildFire content version (0 when none) (`panSysWildfireVersion`).
- `UrlFilteringVersion`: Installed URL filtering version (0 when none installed) (`panSysUrlFilteringVersion`).
- `UrlFilteringDatabase`: URL filtering database in use (`panSysUrlFilteringDatabase`).
- `GlobalProtectClientVersion`: Installed GlobalProtect client package version (0.0.0 when none) (`panSysGlobalProtectClientVersion`).
- `OpswatVersion`: Installed OPSWAT database version, used by GlobalProtect host checks (0 when none) (`panSysOpswatDatafileVersion`).

#### Palo Alto - Security Content Release Dates (15 minutes)

Release dates of the installed application, threat, antivirus and WildFire content, which the published module polls beside the versions in Palo Alto - Security Content Versions. Polled every 900 s.

From `PAN-COMMON-MIB` scalars.
Needs `PAN-COMMON-MIB` imported: the objects this monitor polls are not in ThirdEye's built-in MIB store (whose copy of `PAN-COMMON-MIB` does not define them), and their OIDs and types are those of the published logicvein-monitors module it ports.

- `AppReleaseDate`: Release date of the installed application definitions, as text (`panSysAppReleaseDate`).
- `ThreatReleaseDate`: Release date of the installed threat definitions, as text (`panSysThreatReleaseDate`).
- `AntivirusReleaseDate`: Release date of the installed antivirus definitions, as text (`panSysAvReleaseDate`).
- `WildfireReleaseDate`: Release date of the installed WildFire content, as text (`panSysWfReleaseDate`).

#### Palo Alto - Log Delivery Health (5 minutes)

Incoming and written log rates as the firewall reports them, and logs forwarded to external destinations, dropped from the forwarding queue and failed to send, each as a count since the previous poll and a per-second rate.

From `PAN-COMMON-MIB` scalars.
Needs `PAN-COMMON-MIB` imported: the objects this monitor polls are not in ThirdEye's built-in MIB store (whose copy of `PAN-COMMON-MIB` does not define them), and their OIDs and types are those of the published logicvein-monitors module it ports.

- `IncomingLogRate`: Incoming log rate, as the firewall reports it (`panDeviceIncomingLogRate`).
- `WriteLogRate`: Log write rate, as the firewall reports it (`panDeviceWriteLogRate`).
- `Forwarded`: Logs forwarded to external destinations since the previous poll (`delta(panDeviceLoggingExtFwdCount)`).
- `ForwardedPerSec`: Logs forwarded to external destinations, per second (`rate(panDeviceLoggingExtFwdCount)`).
- `QueueDrops`: Logs dropped from the external forwarding queue since the previous poll (`delta(panDeviceLoggingExtFwdQueueDrop)`).
- `QueueDropsPerSec`: Logs dropped from the external forwarding queue, per second (`rate(panDeviceLoggingExtFwdQueueDrop)`).
- `SendErrors`: External forwarding send errors since the previous poll (`delta(panDeviceLoggingExtFwdStatsSendErr)`).
- `SendErrorsPerSec`: External forwarding send errors, per second (`rate(panDeviceLoggingExtFwdStatsSendErr)`).

#### Palo Alto - External Log Forwarding Queues (5 minutes)

Per external log type: logs enqueued, sent and dropped, each as a count since the previous poll and a per-second rate, with the current queue depth and the firewall's one minute average send rate.

From `PAN-COMMON-MIB::panDeviceLoggingExtFwdStatsTable`, one row per `panDeviceLoggingExtFwdStatsTableType`.
Needs `PAN-COMMON-MIB` imported: the objects this monitor polls are not in ThirdEye's built-in MIB store (whose copy of `PAN-COMMON-MIB` does not define them), and their OIDs and types are those of the published logicvein-monitors module it ports.

- `Enqueued`: Logs enqueued since the previous poll (`delta(panDeviceLoggingExtFwdStatsTableEnqueueCount)`).
- `EnqueuedPerSec`: Logs enqueued, per second (`rate(panDeviceLoggingExtFwdStatsTableEnqueueCount)`).
- `Sent`: Logs sent since the previous poll (`delta(panDeviceLoggingExtFwdStatsTableSendCount)`).
- `SentPerSec`: Logs sent, per second (`rate(panDeviceLoggingExtFwdStatsTableSendCount)`).
- `Dropped`: Logs dropped since the previous poll (`delta(panDeviceLoggingExtFwdStatsTableDropCount)`).
- `DroppedPerSec`: Logs dropped, per second (`rate(panDeviceLoggingExtFwdStatsTableDropCount)`).
- `QueueDepth`: Logs waiting in the queue (typed Counter64 in the published module, but a current depth, so saved as it is) (`panDeviceLoggingExtFwdStatsTableQueueDepth`).
- `SendRate1Min`: Send rate averaged over the last minute, as the firewall reports it (`panDeviceLoggingExtFwdStatsTable1minAvgSendRate`).

#### Palo Alto - Log Collector Connections (5 minutes)

Each connection to a log collector: its address, hostname and status, labelled by connection type as the published module labels it.

From `PAN-COMMON-MIB::panDeviceLoggingCollectorConnectionTable`, one row per `panDeviceLoggingCollectorConnectionType`.
Needs `PAN-COMMON-MIB` imported: the objects this monitor polls are not in ThirdEye's built-in MIB store (whose copy of `PAN-COMMON-MIB` does not define them), and their OIDs and types are those of the published logicvein-monitors module it ports.

- `Address`: Log collector address (`panDeviceLoggingCollectorConnectionIP`).
- `Hostname`: Log collector hostname (`panDeviceLoggingCollectorConnectionHostname`).
- `Status`: Connection status, as text (`panDeviceLoggingCollectorConnectionStatus`).

#### Palo Alto - Log Storage and Retention (5 minutes)

Log database disk use, retention and quota per log type, every 300 s as the published module polls it. This is log database capacity, not a replacement for Palo Alto - Storage.

From `PAN-COMMON-MIB::panDeviceLoggingLogUsageTable`, one row per `panDeviceLoggingLogUsageLogType`.
Needs `PAN-COMMON-MIB` imported: the objects this monitor polls are not in ThirdEye's built-in MIB store (whose copy of `PAN-COMMON-MIB` does not define them), and their OIDs and types are those of the published logicvein-monitors module it ports.

- `DiskSpace`: Disk space the log type uses, as text (`panDeviceLoggingDiskUsageDiskSpace`).
- `Retention`: Retention of the log type, as the firewall reports it (`panDeviceLoggingDiskUsageRetention`).
- `QuotaPct`: Quota of the log type, percent, as text (`panDeviceLoggingDiskQuotaPct`).
- `Quota`: Quota of the log type, as text (`panDeviceLoggingDiskQuota`).

### Palo Alto - Health and Performance Collection

#### Palo Alto - CPU Load (5 minutes)

Load per processor (management and data plane), averaged over the last minute, labelled by the processor's description from hrDeviceTable (same index), as TES's own Palo Alto CPU template does.

From `HOST-RESOURCES-MIB::hrProcessorTable`, one row per `hrDeviceDescr`.
Covers the TES built-in templates *Palo Alto - CPU-UseRate*, *CPU Stats*: it polls every object they poll, under its own names.

- `LoadPct`: Share of the last minute the processor was not idle, percent (`hrProcessorLoad`).

#### Palo Alto - Storage (5 minutes)

Use of each memory and disk storage area the device reports.

From `HOST-RESOURCES-MIB::hrStorageTable`, one row per `hrStorageDescr`.
Covers the TES built-in templates *Palo Alto - MemoryUsage*, *Storage Stats*: it polls every object they poll, under its own names.

- `UsedPct`: Used as a percentage of the storage area's size (`($hrStorageUsed / $hrStorageSize) * 100`).
- `SizeBytes`: Size, bytes (`$hrStorageSize * $hrStorageAllocationUnits`).
- `UsedBytes`: Used, bytes (`$hrStorageUsed * $hrStorageAllocationUnits`).

`delta(x)` is the change in counter `x` between the last two polls; `rate(x)` is that change divided by the monitor's polling period in seconds (TES's `$pollingSecs`), a per-second rate.

## Expected variations

A table monitor returns no rows when the device lacks the feature or hardware it covers. An empty table does not necessarily indicate a collection failure: first confirm that the feature is present and configured on the device.

- HA state depends on an HA configuration and peer; GlobalProtect values on configured gateways; Panorama connection values on Panorama management; log forwarding queues and collector connections on external forwarding and collector configuration; per-VSYS values on the virtual system configuration and any configured session limits.
- A VSYS utilization or maximum of zero can mean that no per-VSYS session limit is configured, not that the virtual system has no capacity.
- Content versions such as `0`, `0.0.0` or `unknown` can mean that the package is not installed, rather than that it is stale.
- Without a current PAN-COMMON-MIB imported, the monitors that name it under Included monitors collect nothing; the others are unaffected.

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
4. Confirm that the firewall's SNMP configuration, and any permitted-IP list on its management interface profile, allows queries from the ThirdEye server.
5. Confirm that the MIB modules listed under Requirements are present in ThirdEye.
6. Wait for the applicable collection interval after making a correction.

If scalar metrics work but a table monitor is empty, confirm that the corresponding feature is configured before treating the result as an error.
