# FortiGate Operational and Health and Performance Collections

Last updated: 2026-10-30

Tested against: FortiGate-VM64-KVM running FortiOS 7.6.7

These monitor sets are for: Fortinet FortiGate firewalls.

The folder holds two sets; assign both to the same devices:

- **FortiGate - Operational Collection** (17 monitors) covers what the device does (its role's state, load and services).
- **FortiGate - Health and Performance Collection** (2 monitors) covers the device itself (CPU, memory, storage and hardware).

The collections are a starting point. They define no alert policies or thresholds.

Interface traffic and errors are not included: assign **Standard Network Interfaces - Health and Performance Collection** to the same devices for those.

## Requirements

Before importing the monitor sets, confirm that:

- The device has been added to the ThirdEye inventory.
- ThirdEye can reach the device's SNMP agent, normally over UDP port 161.
- A working SNMP credential has been assigned to the device in ThirdEye. SNMPv3 with authentication and privacy is recommended.
- SNMP administrative access is enabled on the FortiGate interface through which ThirdEye connects.
- The FortiGate SNMP community or SNMPv3 user permits queries from the ThirdEye server.
- The MIB modules listed below are loaded in ThirdEye.

### MIB modules

The collections use these modules:

- `FORTINET-CORE-MIB`
- `FORTINET-FORTIGATE-MIB`

ThirdEye's built-in MIB store already includes all of these modules. FortiGate - HA All Checksum, FortiGate - SD-WAN Link Usage, FortiGate - Configuration Changes, FortiGate - FortiGuard Contracts and FortiGate - FortiGuard Packages need the vendor's current files of `FORTINET-CORE-MIB` and `FORTINET-FORTIGATE-MIB` imported with their dependencies: the objects they poll are not in ThirdEye's built-in copies. Without that import those monitors collect nothing; the other monitors are unaffected. The MIB files are not included with these collections; to install or update a module, obtain the files as described below and import them with their dependencies.

### Obtaining the Fortinet MIBs

Download both MIB files directly from the FortiGate GUI:

1. Open **System > SNMP**.
2. Select **Download FortiGate MIB File**.
3. Select **Download Fortinet Core MIB File**.

Both files are required because the FortiGate MIB uses definitions from the Fortinet Core MIB.

For additional information, see Fortinet's documentation for [FortiGate MIB files](https://docs.fortinet.com/document/fortigate/7.6.2/administration-guide/608160/mib-files).

## Installation

1. Import the MIB modules listed under Requirements, for the monitors that need them.
2. In **Inventory**, use **Tools > SNMP System Info** to verify that ThirdEye can query each target device.
3. Go to **Monitors > Sets** and import `FortiGate - Operational Collection.3em` and `FortiGate - Health and Performance Collection.3em`.
4. Assign **FortiGate - Operational Collection** and **FortiGate - Health and Performance Collection** to the appropriate devices.
5. Wait at least 15 minutes so every monitor has had a collection opportunity, then confirm that current data is present for the applicable monitors.

Automatic assignment to newly discovered devices is disabled in both sets. Review the sets before enabling automatic assignment in your environment.

These host queries match the devices the sets are meant for: `sysObjectID=1.3.6.1.4.1.12356.*`.

## Included monitors

**FortiGate - Operational Collection** contains 17 SNMP monitors and **FortiGate - Health and Performance Collection** contains 2 SNMP monitors. Metrics carry friendly names; each lists the MIB object it reads or the formula it computes.

### FortiGate - Operational Collection

#### FortiGate - HA Configuration (5 minutes)

The HA mode, group name and automatic configuration synchronisation setting; cluster members are in FortiGate - HA Members.

From `FORTINET-FORTIGATE-MIB` scalars.

- `HaMode`: High-availability mode (1 standalone, 2 active-active, 3 active-passive) (`fgHaSystemMode`).
- `HaGroup`: HA group name (`fgHaGroupName`).
- `HaAutoSync`: Whether automatic HA configuration synchronisation is enabled (disabled, enabled) (`fgHaAutoSync`).

#### FortiGate - Session Demand (5 minutes)

Active IPv4 and IPv6 sessions and the session setup rates the FortiGate averages over the past 1, 10, 30 and 60 minutes.

From `FORTINET-FORTIGATE-MIB` scalars.

- `Sessions`: Active IPv4 sessions (`fgSysSesCount`).
- `SessionSetupRate`: Average IPv4 session setup rate over the past minute, as the MIB reports it (`fgSysSesRate1`).
- `SessionSetupRate10Min`: Average IPv4 session setup rate over the past 10 minutes, as the MIB reports it (`fgSysSesRate10`).
- `SessionSetupRate30Min`: Average IPv4 session setup rate over the past 30 minutes, as the MIB reports it (`fgSysSesRate30`).
- `SessionSetupRate60Min`: Average IPv4 session setup rate over the past 60 minutes, as the MIB reports it (`fgSysSesRate60`).
- `Sessions6`: Active IPv6 sessions (`fgSysSes6Count`).
- `Session6SetupRate`: Average IPv6 session setup rate over the past minute, as the MIB reports it (`fgSysSes6Rate1`).
- `Session6SetupRate10Min`: Average IPv6 session setup rate over the past 10 minutes (`fgSysSes6Rate10`).
- `Session6SetupRate30Min`: Average IPv6 session setup rate over the past 30 minutes (`fgSysSes6Rate30`).
- `Session6SetupRate60Min`: Average IPv6 session setup rate over the past 60 minutes (`fgSysSes6Rate60`).

#### FortiGate - IPS (5 minutes)

Intrusions detected and blocked per virtual domain, as counts since the previous poll and as rates.

From `FORTINET-FORTIGATE-MIB::fgIpsStatsTable`, one row per `fgVdEntName`.

- `Detected`: Intrusions detected since the previous poll (`delta(fgIpsIntrusionsDetected)`).
- `DetectedPerSec`: Intrusions detected, per second (`rate(fgIpsIntrusionsDetected)`).
- `Blocked`: Intrusions blocked since the previous poll (`delta(fgIpsIntrusionsBlocked)`).
- `BlockedPerSec`: Intrusions blocked, per second (`rate(fgIpsIntrusionsBlocked)`).
- `SignatureDetections`: Signature detections since the previous poll (`delta(fgIpsSignatureDetections)`).
- `SignatureDetectionsPerSec`: Signature detections, per second (`rate(fgIpsSignatureDetections)`).
- `AnomalyDetections`: Anomaly detections since the previous poll (`delta(fgIpsAnomalyDetections)`).
- `AnomalyDetectionsPerSec`: Anomaly detections, per second (`rate(fgIpsAnomalyDetections)`).

#### FortiGate - Session Failure Signals (5 minutes)

Signs that the session table is under stress: ephemeral and expectation sessions (current values), and source NAT port clashes, sync and accept queue-full bursts and direct requests to the FortiGate itself. The MIB describes those last four as counters although it types them as gauges, so they are shown as the change since the previous poll and as rates.

From `FORTINET-FORTIGATE-MIB` scalars.

- `EphemeralSessions`: Current ephemeral sessions (`fgSIAdvSesEphemeralCount`).
- `EphemeralLimit`: Limit of ephemeral sessions (`fgSIAdvSesEphemeralLimit`).
- `EphemeralUsedPct`: Ephemeral sessions as a percentage of the limit (`($EphemeralSessions / $EphemeralLimit) * 100.0`).
- `ExpectationSessions`: Current expectation sessions (`fgSIAdvSesExpCount`).
- `SessionClashes`: New sessions that collided with existing ones since the previous poll (usually a shortage of ports or addresses in a source NAT pool) (`delta(fgSIAdvSesClashCount)`).
- `SessionClashesPerSec`: New sessions that collided with existing ones, per second (`rate(fgSIAdvSesClashCount)`).
- `SyncQueueFull`: Sync queue full events since the previous poll (`delta(fgSIAdvSesSyncQFCount)`).
- `SyncQueueFullPerSec`: Sync queue full events, per second (`rate(fgSIAdvSesSyncQFCount)`).
- `AcceptQueueFull`: Accept queue full events since the previous poll (`delta(fgSIAdvSesAcceptQFCount)`).
- `AcceptQueueFullPerSec`: Accept queue full events, per second (`rate(fgSIAdvSesAcceptQFCount)`).
- `NoListener`: Direct requests from outside to the FortiGate's own stack since the previous poll (a sign of an attack on it) (`delta(fgSIAdvSesNoListenerCount)`).
- `NoListenerPerSec`: Direct requests from outside to the FortiGate's own stack, per second (`rate(fgSIAdvSesNoListenerCount)`).

#### FortiGate - HA Members (5 minutes)

Each HA cluster member's configuration sync state, and which unit was master at the last sync attempt.

From `FORTINET-FORTIGATE-MIB::fgHaStatsTable`, one row per `fgHaStatsHostname`.

- `Serial`: Serial number of the member (`fgHaStatsSerial`).
- `SyncStatus`: Configuration sync status (0 unsynchronized, 1 synchronized) (`fgHaStatsSyncStatus`).
- `MasterSerial`: Serial number of the master at the last sync attempt (`fgHaStatsMasterSerial`).
- `LastSyncSuccess`: When the member last synchronised successfully (DateAndTime) (`fgHaStatsSyncDatimeSucc`).
- `LastSyncFailure`: When a synchronisation of the member last failed (DateAndTime) (`fgHaStatsSyncDatimeUnsucc`).
- `GlobalChecksum`: The member's global configuration checksum; members whose checksums differ are out of sync (`fgHaStatsGlobalChecksum`).

#### FortiGate - HA All Checksum (5 minutes)

Each HA member's fgHaStatsAllChecksum, the configuration checksum the published module polls beside the global one in FortiGate - HA Members.

From `FORTINET-FORTIGATE-MIB::fgHaStatsTable`, one row per `fgHaStatsHostname`.
Needs `FORTINET-CORE-MIB`, `FORTINET-FORTIGATE-MIB` imported: the objects this monitor polls are not in ThirdEye's built-in MIB store (whose copies of `FORTINET-CORE-MIB`, `FORTINET-FORTIGATE-MIB` do not define them), and their OIDs and types are those of the published logicvein-monitors module it ports.

- `AllChecksum`: Configuration checksum the MIB names fgHaStatsAllChecksum (`fgHaStatsAllChecksum`).

#### FortiGate - IPsec Tunnels (5 minutes)

State and traffic of each IPsec tunnel, one row per phase 2 selector labelled by its name, as bit and byte rates and as bytes since the previous poll.

From `FORTINET-FORTIGATE-MIB::fgVpnTunTable`, one row per `fgVpnTunEntPhase2Name`.

- `Phase1`: Phase 1 configuration name (`fgVpnTunEntPhase1Name`).
- `RemoteGateway`: IP address of the tunnel's remote gateway (`fgVpnTunEntRemGwyIp`).
- `LifetimeSecs`: Lifetime of the tunnel's phase 2 security association, seconds (`fgVpnTunEntLifeSecs`).
- `Vdom`: Index of the virtual domain the tunnel belongs to (`fgVpnTunEntVdom`).
- `Status`: Tunnel status (1 down, 2 up) (`fgVpnTunEntStatus`).
- `BitsInPerSec`: Bytes received on the tunnel, as bits per second (`rate(fgVpnTunEntInOctets) * 8`).
- `BitsOutPerSec`: Bytes sent on the tunnel, as bits per second (`rate(fgVpnTunEntOutOctets) * 8`).
- `BytesIn`: Bytes received on the tunnel since the previous poll (`delta(fgVpnTunEntInOctets)`).
- `BytesInPerSec`: Bytes received on the tunnel, per second (`rate(fgVpnTunEntInOctets)`).
- `BytesOut`: Bytes sent on the tunnel since the previous poll (`delta(fgVpnTunEntOutOctets)`).
- `BytesOutPerSec`: Bytes sent on the tunnel, per second (`rate(fgVpnTunEntOutOctets)`).

#### FortiGate - SSL VPN (5 minutes)

SSL VPN state and load per virtual domain: users, tunnels and web sessions, with the most of each seen at one time.

From `FORTINET-FORTIGATE-MIB::fgVpnSslStatsTable`, one row per `fgVdEntName`.

- `Enabled`: Whether SSL VPN is enabled on the virtual domain (`fgVpnSslState`).
- `Users`: Users logged in through SSL VPN (`fgVpnSslStatsLoginUsers`).
- `MaxUsers`: Most users logged in at one time (a high-water mark, not a limit) (`fgVpnSslStatsMaxUsers`).
- `Tunnels`: Active SSL VPN tunnels (`fgVpnSslStatsActiveTunnels`).
- `MaxTunnels`: Most tunnels active at one time (a high-water mark, not a limit) (`fgVpnSslStatsMaxTunnels`).
- `WebSessions`: Active SSL VPN web sessions (`fgVpnSslStatsActiveWebSessions`).
- `MaxWebSessions`: Most web sessions active at one time (`fgVpnSslStatsMaxWebSessions`).

#### FortiGate - SD-WAN Health (5 minutes)

SD-WAN performance SLA health checks per member link: state, and latency, jitter and packet loss averaged over the last 30 probes. FortiOS reports the three measurements as text holding a decimal number.

From `FORTINET-FORTIGATE-MIB::fgVWLHealthCheckLinkTable`, one row per `fgVWLHealthCheckLinkName`.

- `State`: Health check state on the member link (0 alive, 1 dead) (`fgVWLHealthCheckLinkState`).
- `Latency`: Average latency over the last 30 probes, as text (`fgVWLHealthCheckLinkLatency`).
- `Jitter`: Average jitter over the last 30 probes, as text (`fgVWLHealthCheckLinkJitter`).
- `PacketLossPct`: Packet loss over the last 30 probes, percent, as text (`fgVWLHealthCheckLinkPacketLoss`).
- `MemberSeq`: Sequence number of the SD-WAN member the health check measures (`fgVWLHealthCheckLinkSeq`).
- `Vdom`: Virtual domain of the health check (`fgVWLHealthCheckLinkVdom`).

#### FortiGate - SD-WAN Link Usage (5 minutes)

The interface, used bandwidth and MOS (voice quality) result of each SD-WAN health check member; MOS values are present only when the health check measures them.

From `FORTINET-FORTIGATE-MIB::fgVWLHealthCheckLinkTable`, one row per `fgVWLHealthCheckLinkName`.
Needs `FORTINET-CORE-MIB`, `FORTINET-FORTIGATE-MIB` imported: the objects this monitor polls are not in ThirdEye's built-in MIB store (whose copies of `FORTINET-CORE-MIB`, `FORTINET-FORTIGATE-MIB` do not define them), and their OIDs and types are those of the published logicvein-monitors module it ports.

- `Interface`: Name of the member's interface (`fgVWLHealthCheckLinkIfName`).
- `UsedBandwidthIn`: Inbound bandwidth the member uses, as the MIB reports it (`fgVWLHealthCheckLinkUsedBandwidthIn`).
- `UsedBandwidthOut`: Outbound bandwidth the member uses, as the MIB reports it (`fgVWLHealthCheckLinkUsedBandwidthOut`).
- `MosCodec`: Codec the MOS result is calculated for (`fgVWLHealthCheckLinkMOSCodec`).
- `Mos`: Mean opinion score, as text (`fgVWLHealthCheckLinkMOS`).

#### FortiGate - Log Delivery (5 minutes)

Delivery to each log device (FortiAnalyzer, syslog and so on): logs sent, relayed, failed and dropped, as counts since the previous poll and as rates, and logs cached for later sending.

From `FORTINET-FORTIGATE-MIB::fgLogDeviceTable`, one row per `fgLogDeviceName`.

- `Enabled`: Whether the log device is enabled (`fgLogDeviceEnabled`).
- `Sent`: Logs sent since the previous poll (`delta(fgLogDeviceSentCount)`).
- `SentPerSec`: Logs sent, per second (`rate(fgLogDeviceSentCount)`).
- `Relayed`: Logs relayed since the previous poll (`delta(fgLogDeviceRelayedCount)`).
- `RelayedPerSec`: Logs relayed, per second (`rate(fgLogDeviceRelayedCount)`).
- `Failed`: Logs that failed to send since the previous poll (`delta(fgLogDeviceFailedCount)`).
- `FailedPerSec`: Logs that failed to send, per second (`rate(fgLogDeviceFailedCount)`).
- `Dropped`: Logs dropped since the previous poll (`delta(fgLogDeviceDroppedCount)`).
- `DroppedPerSec`: Logs dropped, per second (`rate(fgLogDeviceDroppedCount)`).
- `Cached`: Logs cached for later sending (`fgLogDeviceCachedCount`).

#### FortiGate - Firmware Version (15 minutes)

The FortiOS firmware version, polled apart from the security content versions so a problem with one does not stop the other (as the published module keeps them).

From `FORTINET-FORTIGATE-MIB` scalars.

- `Firmware`: Firmware version (`fgSysVersion`).

#### FortiGate - Security Content Versions (15 minutes)

Antivirus and IPS signature and engine versions.

From `FORTINET-FORTIGATE-MIB` scalars.

- `AntivirusVersion`: Antivirus signature database version (`fgSysVersionAv`).
- `IpsVersion`: IPS signature database version (`fgSysVersionIps`).
- `AntivirusEngineVersion`: Antivirus engine version (`fgSysVersionAvEt`).
- `IpsEngineVersion`: IPS engine version (`fgSysVersionIpsEt`).

#### FortiGate - Firewall Policies (5 minutes)

Packets and bytes each firewall policy matched, as counts since the previous poll and as rates, and when it was last used. One row per policy, often hundreds, so it polls every 5 minutes; correlate the policy ID with the configuration for its name. The published module's README notes that the table can include a policy 0 "No Session Data" placeholder when no policy has session data.

From `FORTINET-FORTIGATE-MIB::fgFwPolStatsTable`, one row per `fgFwPolID`.

- `LastUsed`: When the policy was last used, as the FortiGate reports it (`fgFwPolLastUsed`).
- `Packets`: Packets the policy matched since the previous poll (`delta(fgFwPolPktCountHc)`).
- `PacketsPerSec`: Packets the policy matched, per second (`rate(fgFwPolPktCountHc)`).
- `Bytes`: Bytes the policy matched since the previous poll (`delta(fgFwPolByteCountHc)`).
- `BytesPerSec`: Bytes the policy matched, per second (`rate(fgFwPolByteCountHc)`).

#### FortiGate - Configuration Changes (5 minutes)

Configuration changes, from the configuration serial number, which increments when the configuration changes (as the published module's README describes it), with the configuration checksum and the time of the last change, which is relative to system uptime rather than a wall-clock time.

From `FORTINET-FORTIGATE-MIB` scalars.
Needs `FORTINET-CORE-MIB`, `FORTINET-FORTIGATE-MIB` imported: the objects this monitor polls are not in ThirdEye's built-in MIB store (whose copies of `FORTINET-CORE-MIB`, `FORTINET-FORTIGATE-MIB` do not define them), and their OIDs and types are those of the published logicvein-monitors module it ports.

- `ConfigChanges`: Configuration changes since the previous poll (the change in the configuration serial number) (`delta(fgConfigSerial)`).
- `ConfigChangesPerSec`: Configuration changes, per second (`rate(fgConfigSerial)`).
- `ConfigChecksum`: Checksum of the current configuration (`fgConfigChecksum`).
- `LastChangeTime`: System uptime at the last configuration change (TimeStamp) (`fgConfigLastChangeTime`).

#### FortiGate - FortiGuard Contracts (15 minutes)

Each FortiGuard contract and its expiry, to tell an expired entitlement from a failed package update (FortiGate - FortiGuard Packages).

From `FORTINET-FORTIGATE-MIB::fgLicContractTable`, one row per `fgLicContractDesc`.
Needs `FORTINET-CORE-MIB`, `FORTINET-FORTIGATE-MIB` imported: the objects this monitor polls are not in ThirdEye's built-in MIB store (whose copies of `FORTINET-CORE-MIB`, `FORTINET-FORTIGATE-MIB` do not define them), and their OIDs and types are those of the published logicvein-monitors module it ports.

- `Expiry`: When the contract expires, as text (`fgLicContractExpiry`).

#### FortiGate - FortiGuard Packages (15 minutes)

Each FortiGuard package's version and expiry, and when and how it was last updated and last tried.

From `FORTINET-FORTIGATE-MIB::fgLicVersionTable`, one row per `fgLicVersionDesc`.
Needs `FORTINET-CORE-MIB`, `FORTINET-FORTIGATE-MIB` imported: the objects this monitor polls are not in ThirdEye's built-in MIB store (whose copies of `FORTINET-CORE-MIB`, `FORTINET-FORTIGATE-MIB` do not define them), and their OIDs and types are those of the published logicvein-monitors module it ports.

- `Version`: Package version (`fgLicVersionNumber`).
- `Expiry`: When the package's licence expires, as text (`fgLicVersionExpiry`).
- `LastUpdate`: When the package was last updated, as text (`fgLicVersionUpdTime`).
- `UpdateMethod`: How the package was last updated, as text (`fgLicVersionUpdMethod`).
- `LastAttempt`: When an update was last tried, as text (`fgLicVersionTryTime`).
- `LastAttemptResult`: Result of the last update attempt, as text (`fgLicVersionTryResult`).

### FortiGate - Health and Performance Collection

#### FortiGate - System (5 minutes)

CPU, memory and disk use system-wide.

From `FORTINET-FORTIGATE-MIB` scalars.
Covers the TES built-in templates *Fortigate - CPUUseRate*, *Fortigate - MemoryUsage*: it polls every object they poll, under its own names.

- `CpuPct`: Current CPU usage, percent (`fgSysCpuUsage`).
- `MemoryPct`: Current memory utilization, percent (`fgSysMemUsage`).
- `DiskUsedMB`: Hard disk usage, MB, if a disk is present (`fgSysDiskUsage`).
- `DiskCapacityMB`: Hard disk capacity, MB, if a disk is present (`fgSysDiskCapacity`).
- `DiskUsedPct`: Hard disk used as a percentage of its capacity (`($DiskUsedMB / $DiskCapacityMB) * 100.0`).

#### FortiGate - CPU per Processor (5 minutes)

Usage of each processor, averaged over the last minute.

From `FORTINET-FORTIGATE-MIB::fgProcessorTable`.

- `CpuPct`: Processor usage over the last minute, percent (`fgProcessorUsage`).

`delta(x)` is the change in counter `x` between the last two polls; `rate(x)` is that change divided by the monitor's polling period in seconds (TES's `$pollingSecs`), a per-second rate.

## Expected variations

A table monitor returns no rows when the device lacks the feature or hardware it covers. An empty table does not necessarily indicate a collection failure: first confirm that the feature is present and configured on the device.

- HA tables depend on an HA cluster configuration; IPsec and SSL VPN tables on the corresponding VPN services; SD-WAN data on configured health checks and member links; log delivery data on configured log destinations; FortiGuard contract and package tables on the appliance's licensed services.
- Without the Fortinet MIB import, the monitors that name it under Included monitors collect nothing; the others are unaffected.

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
4. Confirm that SNMP administrative access is enabled on the FortiGate interface ThirdEye connects to, and that the SNMP community or user permits the ThirdEye server.
5. Confirm that the MIB modules listed under Requirements are present in ThirdEye.
6. Wait for the applicable collection interval after making a correction.

If scalar metrics work but a table monitor is empty, confirm that the corresponding feature is configured before treating the result as an error.
