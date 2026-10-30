# Cisco Catalyst 9800 WLC Operational and Health and Performance Collections

Last updated: 2026-10-30

Tested against: Cisco C9800-CL running IOS XE 17.15.4d

These monitor sets are for: Cisco Catalyst 9800 wireless controllers (IOS XE): controller CPU, memory and reload reason, access point state, load and stability, radios, WLANs and RADIUS authentication servers.

The folder holds two sets; assign both to the same devices:

- **Cisco Catalyst 9800 WLC - Operational Collection** (10 monitors) covers what the device does (its role's state, load and services).
- **Cisco Catalyst 9800 WLC - Health and Performance Collection** (1 monitor) covers the device itself (CPU, memory, storage and hardware).

The collections are a starting point. They define no alert policies or thresholds.

Interface traffic and errors are not included: assign **Standard Network Interfaces - Health and Performance Collection** to the same devices for those.

## Requirements

Before importing the monitor sets, confirm that:

- The device has been added to the ThirdEye inventory.
- ThirdEye can reach the device's SNMP agent, normally over UDP port 161.
- A working SNMP credential has been assigned to the device in ThirdEye. SNMPv3 with authentication and privacy is recommended.
- SNMP is enabled on the controller and permits queries from the ThirdEye server.
- The MIB modules listed below are loaded in ThirdEye.

### MIB modules

The collections use these modules:

- `AIRESPACE-WIRELESS-MIB`
- `CISCO-LWAPP-AP-MIB`
- `CISCO-PROCESS-MIB`
- `OLD-CISCO-SYSTEM-MIB`

ThirdEye's built-in MIB store already includes all of these modules. Cisco 9800 WLC - Joined Access Points needs the vendor's current file of `CISCO-LWAPP-AP-MIB` imported with its dependencies: the objects it polls are not in ThirdEye's built-in copies. Without that import those monitors collect nothing; the other monitors are unaffected. The MIB files are not included with these collections; to install or update a module, obtain the files as described below and import them with their dependencies.

### Obtaining the Cisco MIBs

Cisco states that the same Catalyst 9800 MIB package applies across the 9800-80, 9800-40, 9800-L, 9800-CL and Embedded Wireless Controller variants. Obtain the current package and validate it against the controller release before broad use:

1. Open the [Cisco MIB download site](https://cfnng.cisco.com/mibs).
2. Download the Catalyst 9800 MIB archive with the most recent publication date. Cisco notes that the newest archive is identified by its date, not necessarily by the highest version-like filename.
3. Extract the archive and import the required modules and their dependencies into ThirdEye.

For Cisco's SNMP support guidance, MIB compatibility notes and OID validation examples, see [Monitor Catalyst 9800 WLC via SNMP with OIDs](https://www.cisco.com/c/en/us/support/docs/wireless/catalyst-9800-series-wireless-controllers/217460-monitor-catalyst-9800-wlc-via-snmp-with.html).

## Installation

1. Import the MIB modules listed under Requirements, for the monitors that need them.
2. In **Inventory**, use **Tools > SNMP System Info** to verify that ThirdEye can query each target device.
3. Go to **Monitors > Sets** and import `Cisco Catalyst 9800 WLC - Operational Collection.3em` and `Cisco Catalyst 9800 WLC - Health and Performance Collection.3em`.
4. Assign **Cisco Catalyst 9800 WLC - Operational Collection** and **Cisco Catalyst 9800 WLC - Health and Performance Collection** to the appropriate devices.
5. Wait at least 15 minutes so every monitor has had a collection opportunity, then confirm that current data is present for the applicable monitors.

Automatic assignment to newly discovered devices is disabled in both sets. Review the sets before enabling automatic assignment in your environment.

These host queries match the devices the sets are meant for: `sysDescr=*C9800*`.

## Included monitors

**Cisco Catalyst 9800 WLC - Operational Collection** contains 10 SNMP monitors and **Cisco Catalyst 9800 WLC - Health and Performance Collection** contains 1 SNMP monitor. Metrics carry friendly names; each lists the MIB object it reads or the formula it computes.

### Cisco Catalyst 9800 WLC - Operational Collection

#### Cisco 9800 WLC - Reload Reason (15 minutes)

Why the controller last reloaded, as the controller reports it. The software image and uptime are available from the device inventory and SNMP System Info, so only the reason is polled.

From `OLD-CISCO-SYSTEM-MIB` scalars.

- `ReloadReason`: Reason for the last reload, as text (`whyReload`).

#### Cisco 9800 WLC - Joined Access Points (5 minutes)

The number of wireless access points joined to the controller.

From `CISCO-LWAPP-AP-MIB` scalars.
Needs `CISCO-LWAPP-AP-MIB` imported: the objects this monitor polls are not in ThirdEye's built-in MIB store (whose copy of `CISCO-LWAPP-AP-MIB` does not define them), and their OIDs and types are those of the published logicvein-monitors module it ports.

- `JoinedAps`: Access points currently joined to the controller (`cLApGlobalAPConnectCount`).

#### Cisco 9800 WLC - Access Point State (5 minutes)

Each joined wireless access point's operational and administrative state.

From `AIRESPACE-WIRELESS-MIB::bsnAPTable`, one row per `bsnAPName`.

- `OperStatus`: Operational state (associated, disassociating, downloading) (`bsnAPOperationStatus`).
- `AdminStatus`: Administrative state (enable, disable) (`bsnAPAdminStatus`).

#### Cisco 9800 WLC - Access Point Inventory (15 minutes)

Each joined access point's address, location, model, serial number, software version, primary controller and radio count, polled every 15 minutes as they rarely change.

From `AIRESPACE-WIRELESS-MIB::bsnAPTable`, one row per `bsnAPName`.

- `IpAddress`: IP address of the access point (0 when the controller runs in Layer 2 mode) (`bsnApIpAddress`).
- `Location`: Location configured for the access point (`bsnAPLocation`).
- `Model`: Access point model (`bsnAPModel`).
- `SerialNumber`: Access point serial number (`bsnAPSerialNumber`).
- `SoftwareVersion`: Access point software version (`bsnAPSoftwareVersion`).
- `PrimaryController`: Name of the controller configured as the access point's primary (`bsnAPPrimaryMwarName`).
- `RadioSlots`: Number of radio interfaces on the access point (`bsnAPNumOfSlots`).

#### Cisco 9800 WLC - Access Point Load (5 minutes)

Each joined access point's CPU and memory use, current and averaged over the controller's statistics interval, and its associated and active clients.

From `CISCO-LWAPP-AP-MIB::cLApTable`, one row per `cLApName`.

- `CpuPct`: Access point CPU in use, percent (`cLApCpuCurrentUsage`).
- `CpuAvgPct`: Access point CPU in use averaged over the controller's statistics interval, percent (`cLApCpuAverageUsage`).
- `MemoryPct`: Access point memory in use, percent (`cLApMemoryCurrentUsage`).
- `MemoryAvgPct`: Access point memory in use averaged over the controller's statistics interval, percent (`cLApMemoryAverageUsage`).
- `AssociatedClients`: Clients associated with the access point (`cLApAssociatedClientCount`).
- `ActiveClients`: Active clients on the access point (`cLApActiveClientCount`).

#### Cisco 9800 WLC - Access Point Stability (5 minutes)

Each joined access point's uptime and time since it joined the controller, its last reboot reason and administrative state, abnormal offline events, echo response time and lost echo responses.

From `CISCO-LWAPP-AP-MIB::cLApTable`, one row per `cLApName`.

- `Uptime`: Time since the access point last rebooted, hundredths of a second (TimeTicks, saved raw) (`cLApUpTime`).
- `JoinUptime`: Time since the access point last joined the controller, hundredths of a second (TimeTicks, saved raw) (`cLLwappUpTime`).
- `LastRebootReason`: Reason for the access point's last reboot (`cLApLastRebootReason`).
- `AdminEnabled`: Whether the access point is administratively up (TruthValue: 1 true, 2 false) (`cLApAdminStatus`).
- `AbnormalOfflines`: Abnormal offline events counted for the access point, as reported (`cLApAbnormalOfflineCount`).
- `EchoResponseMs`: Average time from echo request to echo response, milliseconds (`cLApHeartBeatRspAvgTime`).
- `EchoResponsesLost`: Echo responses lost between the controller and the access point since the previous poll (`delta(cLApEchoResponseLossCount)`).
- `EchoResponsesLostPerSec`: Echo responses lost between the controller and the access point, per second (`rate(cLApEchoResponseLossCount)`).
- `ControllerJoinedAps`: Access points joined to the controller: the MIB's deprecated per-row copy of the controller-wide count, the same on every row (`cLApConnectCount`).

#### Cisco 9800 WLC - Radios (5 minutes)

Each access point radio's slot, band, channel, transmit power level, active WLANs, operational and administrative state, and users. Rows are identified by the access point's MAC address and the radio slot (the table index); the table has no name column.

From `AIRESPACE-WIRELESS-MIB::bsnAPIfTable`.

- `Slot`: Radio slot number (`bsnAPIfSlotId`).
- `Band`: Radio type (dot11b, which includes 802.11b/g; dot11a; uwb) (`bsnAPIfType`).
- `Channel`: Channel in use (`bsnAPIfPhyChannelNumber`).
- `TxPowerLevel`: Transmit power level in use (1 to 8) (`bsnAPIfPhyTxPowerLevel`).
- `Wlans`: WLANs currently active on the radio (`bsnAPIfNumberOfVaps`).
- `OperStatus`: Operational state (up, down) (`bsnAPIfOperStatus`).
- `AdminStatus`: Administrative state (enable, disable) (`bsnAPIfAdminStatus`).
- `Users`: Users associated with the radio (typed Counter32 in the MIB, but a current count) (`bsnApIfNoOfUsers`).

#### Cisco 9800 WLC - Radio Load (5 minutes)

Each access point radio's receive, transmit and channel utilization, and its clients and clients with poor signal-to-noise ratio at the last measurement interval. Rows are identified as in Cisco 9800 WLC - Radios.

From `AIRESPACE-WIRELESS-MIB::bsnAPIfLoadParametersTable`.

- `ReceiveBusyPct`: Time the radio's receiver is busy, percent (`bsnAPIfLoadRxUtilization`).
- `TransmitBusyPct`: Time the radio's transmitter is busy, percent (`bsnAPIfLoadTxUtilization`).
- `ChannelUtilizationPct`: Channel utilization, percent (`bsnAPIfLoadChannelUtilization`).
- `Clients`: Clients on the radio at the last measurement interval (`bsnAPIfLoadNumOfClients`).
- `PoorSnrClients`: Clients with a poor signal-to-noise ratio at the last measurement interval (`bsnAPIfPoorSNRClients`).

#### Cisco 9800 WLC - WLANs (5 minutes)

Each WLAN's administrative state, radio policy, associated clients, interface and SSID broadcast setting, labelled by SSID.

From `AIRESPACE-WIRELESS-MIB::bsnDot11EssTable`, one row per `bsnDot11EssSsid`.

- `WlanId`: WLAN identifier (the table index) (`bsnDot11EssIndex`).
- `AdminStatus`: Administrative state (enable, disable) (`bsnDot11EssAdminStatus`).
- `RadioPolicy`: Radio types the WLAN is offered on (`bsnDot11EssRadioPolicy`).
- `Clients`: Clients currently associated with the WLAN (typed Counter32 in the MIB, but a current count) (`bsnDot11EssNumberOfMobileStations`).
- `Interface`: Name of the interface the WLAN uses (`bsnDot11EssInterfaceName`).
- `BroadcastSsid`: Whether the SSID is broadcast (enable, disable) (`bsnDot11EssBroadcastSsid`).

#### Cisco 9800 WLC - RADIUS Authentication Servers (5 minutes)

Each configured RADIUS authentication server's state, port and retransmit timeout, labelled by its address. Shared secrets and key-wrapping material are not polled.

From `AIRESPACE-WIRELESS-MIB::bsnRadiusAuthServerTable`, one row per `bsnRadiusAuthServerAddress`.

- `ServerIndex`: Server index (the table index) (`bsnRadiusAuthServerIndex`).
- `Port`: UDP port the controller sends requests to (`bsnRadiusAuthClientServerPortNumber`).
- `AdminStatus`: Whether the server is enabled (enable, disable) (`bsnRadiusAuthServerStatus`).
- `RetransmitTimeoutSecs`: Seconds before a request times out and is retransmitted (`bsnRadiusAuthServerRetransmitTimeout`).
- `RowStatus`: Row status of the server entry (`bsnRadiusAuthServerRowStatus`).

### Cisco Catalyst 9800 WLC - Health and Performance Collection

#### Cisco 9800 WLC - CPU and Memory (5 minutes)

Controller CPU busy over 1 and 5 minutes, and memory used, free and in use as a percentage, per processor, from the original CISCO-PROCESS-MIB objects ThirdEye's own Catalyst 9800 WLC templates poll.

From `CISCO-PROCESS-MIB::cpmCPUTotalTable`.
Covers the TES built-in templates *Cisco CPU*, *Cisco Catalyst 9800 WLC - CPU Utilization [1min Avg]*, *Cisco Catalyst 9800 WLC - CPU Utilization [5min Avg]*, *Cisco Catalyst 9800 WLC - Memory Utilization*: it polls every object they poll, under its own names.

- `Busy1MinPct`: CPU busy over the last minute, percent (`cpmCPUTotal1min`).
- `Busy5MinPct`: CPU busy over the last 5 minutes, percent (`cpmCPUTotal5min`).
- `MemoryUsedKB`: Memory in use, kilobytes (`cpmCPUMemoryUsed`).
- `MemoryFreeKB`: Memory free, kilobytes (`cpmCPUMemoryFree`).
- `MemoryPct`: Memory in use as a percentage of used plus free (`($MemoryUsedKB / ($MemoryUsedKB + $MemoryFreeKB)) * 100.0`).

`delta(x)` is the change in counter `x` between the last two polls; `rate(x)` is that change divided by the monitor's polling period in seconds (TES's `$pollingSecs`), a per-second rate.

## Expected variations

A table monitor returns no rows when the device lacks the feature or hardware it covers. An empty table does not necessarily indicate a collection failure: first confirm that the feature is present and configured on the device.

- The Catalyst 9800 favours streaming telemetry, and not every object of the older AireOS MIBs is implemented on every IOS XE release and access point combination; a field the combination does not implement can show `-`, which means unsupported, not zero.
- Access point and radio tables depend on the access points currently joined; WLAN rows on configured WLANs, whose text fields can show `-` on some releases while their state and client counts work; RADIUS rows on configured authentication servers.
- Radio rows are identified by the access point's MAC address and the radio slot, which ThirdEye can show as OID digits, since the radio tables have no name column.
- A first broad collection can time out while later cycles succeed; confirm a repeated failure before changing the set.
- High-availability state is not included: it needs CISCO-LWAPP-HA-MIB imported and validated against an HA pair.
- Without a current CISCO-LWAPP-AP-MIB imported, Cisco 9800 WLC - Joined Access Points collects nothing; Cisco 9800 WLC - Access Point Stability still reports the joined count on each access point row, from a deprecated object.

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
4. Confirm that SNMP is enabled on the controller and that its SNMP configuration permits the ThirdEye server.
5. On the controller, `show snmp mib | include <object-name>` shows whether its IOS XE release implements an object the set polls.
6. Confirm that the MIB modules listed under Requirements are present in ThirdEye.
7. Wait for the applicable collection interval after making a correction.

If scalar metrics work but a table monitor is empty, confirm that the corresponding feature is configured before treating the result as an error.
