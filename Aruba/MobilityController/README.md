# ArubaOS Mobility Controller Operational and Health and Performance Collections

Last updated: 2026-10-24

Tested against: Aruba 7010 running ArubaOS 8.12.0.1 SSR (build 89864)

These monitor sets are for: Aruba Mobility Controllers and gateways on ArubaOS.

The folder holds two sets; assign both to the same devices:

- **ArubaOS Mobility Controller - Operational Collection** (11 monitors) covers what the device does (its role's state, load and services).
- **ArubaOS Mobility Controller - Health and Performance Collection** (5 monitors) covers the device itself (CPU, memory, storage and hardware).

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

- `WLSX-AUTH-MIB`
- `WLSX-HA-MIB`
- `WLSX-SYSTEMEXT-MIB`
- `WLSX-USER-MIB`
- `WLSX-WLAN-MIB`

ThirdEye's built-in MIB store already includes all of these modules, so no MIB import is needed. The MIB files are not included with these collections; to install or update a module, obtain the files as described below and import them with their dependencies.

### Obtaining the ArubaOS MIBs

Download the MIB package that corresponds to the ArubaOS release being monitored from the Aruba Support site:

1. Sign in to the Aruba Support site.
2. Open **Download Software > ArubaOS**.
3. Open the folder for the required ArubaOS release.
4. Download the corresponding MIB archive and extract it locally.

Import the WLSX modules together with `ARUBA-TC`, `ARUBA-MIB`, and their standard SNMP dependencies so ThirdEye can resolve the Aruba textual conventions used by the monitor set.

For additional information, see HPE Aruba Networking's documentation for [ArubaOS MIB files](https://arubanetworking.hpe.com/techdocs/ArubaOS/AOS_8x_WebHelp/Content/arubaos-solutions/manage-utilities/mib-file.htm).

## Installation

1. In **Inventory**, use **Tools > SNMP System Info** to verify that ThirdEye can query each target device.
2. Go to **Monitors > Sets** and import `ArubaOS Mobility Controller - Operational Collection.3em` and `ArubaOS Mobility Controller - Health and Performance Collection.3em`.
3. Assign **ArubaOS Mobility Controller - Operational Collection** and **ArubaOS Mobility Controller - Health and Performance Collection** to the appropriate devices.
4. Wait at least 15 minutes so every monitor has had a collection opportunity, then confirm that current data is present for the applicable monitors.

Automatic assignment to newly discovered devices is disabled in both sets. Review the sets before enabling automatic assignment in your environment.

These host queries match the devices the sets are meant for: `sysObjectID=1.3.6.1.4.1.14823.*`.

## Included monitors

**ArubaOS Mobility Controller - Operational Collection** contains 11 SNMP monitors and **ArubaOS Mobility Controller - Health and Performance Collection** contains 5 SNMP monitors. Metrics carry friendly names; each lists the MIB object it reads or the formula it computes.

### ArubaOS Mobility Controller - Operational Collection

#### Aruba Controller - WLANs (1 minute)

Clients and access points per advertised network (ESSID). The ESSID table has no name column besides its index, so the rows carry no label and TES shows each ESSID as OID digits (its length, then the character codes); a label of the index object showed the same digits on a controller.

From `WLSX-WLAN-MIB::wlsxWlanESSIDTable`.

- `Clients`: Stations connected to the ESSID (`wlanESSIDNumStations`).
- `AccessPointsUp`: Access points advertising the ESSID (`wlanESSIDNumAccessPointsUp`).
- `AccessPointsDown`: Access points configured for the ESSID that are not operational (`wlanESSIDNumAccessPointsDown`).
- `EncryptionType`: Encryption the ESSID uses (`wlanESSIDEncryptionType`).

#### Aruba Controller - Radios (1 minute)

Load per access point radio: associated clients, utilization and channel. One row per radio, so this monitor is large on big deployments.

From `WLSX-WLAN-MIB::wlsxWlanRadioTable`, one row per `wlanAPRadioAPName`.

- `Clients`: Clients associated to the radio (`wlanAPRadioNumAssociatedClients`).
- `UtilizationPct`: Radio utilization as a percentage of its total capacity (`wlanAPRadioUtilization`).
- `Channel`: Channel the radio is operating on (`wlanAPRadioChannel`).
- `RadioType`: Radio PHY type (`wlanAPRadioType`).
- `Mode`: Radio mode (`wlanAPRadioMode`).
- `HtMode`: High-throughput mode (`wlanAPRadioHTMode`).
- `TransmitPowerX10`: Transmit power, as the object reports it (its name says ten times the power) (`wlanAPRadioTransmitPower10x`).
- `ActiveBssids`: Active BSSIDs on the radio (`wlanAPRadioNumActiveBSSIDs`).

#### Aruba Controller - Access Point Stability (1 minute)

Each access point's status and stability: time since it last bootstrapped with the controller, and how often it has bootstrapped and rebooted, with its group, model and software.

From `WLSX-WLAN-MIB::wlsxWlanAPTable`, one row per `wlanAPName`.

- `Status`: AP status (the MIB's ArubaAPStatus values) (`wlanAPStatus`).
- `Group`: AP group (`wlanAPGroupName`).
- `Model`: AP model (`wlanAPModelName`).
- `Uptime`: Time since the AP last bootstrapped with the controller, hundredths of a second (TimeTicks, saved raw) (`wlanAPUpTime`).
- `Bootstraps`: Times the AP has bootstrapped with the controller (a running total) (`wlanAPNumBootstraps`).
- `Reboots`: Times the AP has rebooted (a running total) (`wlanAPNumReboots`).
- `Software`: AP software version (`wlanAPSwVersion`).
- `IpAddress`: Access point IP address (`wlanAPIpAddress`).
- `WarmReboots`: Warm reboots of the access point (a running total) (`wlanAPNumWarmReboots`).

#### Aruba Controller - Authentication Servers (1 minute)

State of each authentication server the controller uses: its type, whether it is enabled and in service, its average response time and the requests outstanding. Request counts are in Aruba Controller - Authentication Requests.

From `WLSX-AUTH-MIB::wlsxAuthenticationServerTable`, one row per `authServerAddress`.

- `State`: Server state (the MIB's ArubaEnableValue values) (`authServerState`).
- `Serving`: Whether the server is in service (`authServerInservice`).
- `Type`: Authentication server type (`authServerType`).
- `AvgResponseMs`: Average response time, milliseconds (`authServerAvgResponseTime`).
- `Outstanding`: Requests currently outstanding (`authServerOutStandingRequests`).

#### Aruba Controller - Authentication Requests (1 minute)

Requests each authentication server handled, succeeded, failed and timed out, each as a count since the previous poll and a per-second rate.

From `WLSX-AUTH-MIB::wlsxAuthenticationServerTable`, one row per `authServerAddress`.

- `Requests`: Requests sent to the server since the previous poll (`delta(authServerUsageCount)`).
- `RequestsPerSec`: Requests sent to the server, per second (`rate(authServerUsageCount)`).
- `Successes`: Successful responses since the previous poll (`delta(authServerSuccessfullAuths)`).
- `SuccessesPerSec`: Successful responses, per second (`rate(authServerSuccessfullAuths)`).
- `Failures`: Failure responses since the previous poll (`delta(authServerFailedAuths)`).
- `FailuresPerSec`: Failure responses, per second (`rate(authServerFailedAuths)`).
- `Timeouts`: Requests that timed out since the previous poll (`delta(authServerTimeouts)`).
- `TimeoutsPerSec`: Requests that timed out, per second (`rate(authServerTimeouts)`).

#### Aruba Controller - HA Profiles (1 minute)

Each high-availability profile: this controller's membership, the profile's state and role, and whether state sync and inter-controller heartbeats are enabled, with the active, standby and total access points from wlsxHighAvalabilityApTable, matched to the profile rows by row index as the published logicvein-monitors module reads them. The profile's pre-shared key is not polled.

From `WLSX-HA-MIB::wlsxHighAvalabilityConfigTable`, one row per `haMembership`.

- `State`: HA state of the profile (`haState`).
- `Role`: HA role of the controller (`haRole`).
- `StateSync`: Whether state synchronisation is enabled (`haStateSync`).
- `Heartbeats`: Whether inter-controller heartbeats are enabled (`haIntercontrollerHbt`).
- `ActiveAps`: Access points active on this controller (`haActiveAPs`).
- `StandbyAps`: Access points on standby on this controller (`haStandbyAPs`).
- `TotalAps`: Access points in the profile (`haTotalAPs`).

#### Aruba Controller - HA Heartbeats (1 minute)

Heartbeats between this controller and each active controller in its HA group: requests sent and responses received, as counts since the previous poll and as rates, and the heartbeats last missed.

From `WLSX-HA-MIB::wlsxIntercontrollerHbtTable`, one row per `haActiveCtrlIp`.

- `RequestsSent`: Heartbeat requests sent since the previous poll (`delta(haTotalHbtRequestsSent)`).
- `RequestsSentPerSec`: Heartbeat requests sent, per second (`rate(haTotalHbtRequestsSent)`).
- `ResponsesReceived`: Heartbeat responses received since the previous poll (`delta(haTotalHbtResponsesRcvd)`).
- `ResponsesReceivedPerSec`: Heartbeat responses received, per second (`rate(haTotalHbtResponsesRcvd)`).
- `LastMissed`: Heartbeats last missed (`haLastMissedHbtCnt`).
- `ReferenceCount`: Reference count of the heartbeat session (`haReferenceCnt`).
- `LastMissedTime`: When a heartbeat was last missed, as text (`haLastHbtMissedTime`).

#### Aruba Controller - Wireless Demand (1 minute)

Load on the controller: access points connected, stations associated, and users by authentication type.

From `WLSX-USER-MIB`, `WLSX-WLAN-MIB` scalars.

- `AccessPoints`: Access points connected to the controller (`wlsxWlanTotalNumAccessPoints`).
- `Stations`: Stations associated to the controller (`wlsxWlanTotalNumStationsAssociated`).
- `Users`: Users in total (`wlsxTotalNumOfUsers`).
- `Users8021x`: 802.1X users (`wlsxNumOfUsers8021x`).
- `VpnUsers`: VPN users (`wlsxNumOfUsersVPN`).
- `CaptivePortalUsers`: Captive portal users (`wlsxNumOfUsersCP`).
- `MacUsers`: Users authenticated by MAC address (`wlsxNumOfUsersMAC`).
- `Stateful8021xUsers`: Users authenticated by stateful 802.1X (`wlsxNumOfUsersStateful8021x`).

#### Aruba Controller - Role and Configuration State (1 minute)

The controller's role, its conductor's address, and the configuration IDs and whether configuration updates from the conductor are enabled, every minute as the published logicvein-monitors module polls them.

From `WLSX-SYSTEMEXT-MIB` scalars.

- `Role`: Role of this controller in the Aruba domain (the MIB's ArubaSwitchRole values) (`wlsxSysExtSwitchRole`).
- `ConductorIp`: Address of the conductor (formerly master) controller (`wlsxSysExtSwitchConductorIp`).
- `ConductorConfigId`: Configuration ID from the conductor (Mobility Conductor) (`wlsxSysExtMMSConfigID`).
- `ControllerConfigId`: Configuration ID of the controller (`wlsxSysExtControllerConfigID`).
- `ConductorConfigUpdates`: Whether configuration updates from the conductor are enabled (`wlsxSysExtIsMMSConfigUpdateEnabled`).

#### Aruba Controller - Software and Reload State (15 minutes)

Software and hardware versions and the reason for the last reload. These change rarely, so the monitor polls every 900 s.

From `WLSX-SYSTEMEXT-MIB` scalars.

- `Software`: Software version (`wlsxSysExtSwVersion`).
- `Hardware`: Hardware version (`wlsxSysExtHwVersion`).
- `LastReloadReason`: Reason for the last controller reload (`wlsxSysExtSwitchLastReload`).

#### Aruba Controller - Licences (15 minutes)

Each installed licence: the service it enables, when it was installed and when it expires. Several licence keys can enable the same service, so labels may repeat; the licence key itself is not saved. Polled every 900 s.

From `WLSX-SYSTEMEXT-MIB::wlsxSysExtSwitchLicenseTable`, one row per `sysExtLicenseService`.

- `Installed`: Licence installation time, as text (`sysExtLicenseInstalled`).
- `Expires`: Licence expiry time, as text (`sysExtLicenseExpires`).
- `Flags`: Licence flags, as text (`sysExtLicenseFlags`).

### ArubaOS Mobility Controller - Health and Performance Collection

#### Aruba Controller - CPU Utilization (1 minute)

Load per processor, averaged over the last minute.

From `WLSX-SYSTEMEXT-MIB::wlsxSysExtProcessorTable`, one row per `sysExtProcessorDescr`.
Covers the TES built-in template *Aruba WLC - CPU Utilization*: it polls every object that template polls, under its own names.

- `LoadPct`: Share of the last minute the processor was not idle, percent (`sysExtProcessorLoad`).

#### Aruba Controller - Memory (1 minute)

Used and free memory.

From `WLSX-SYSTEMEXT-MIB::wlsxSysExtMemoryTable`.
Covers the TES built-in template *Aruba WLC - Memory Utilization*: it polls every object that template polls, under its own names.

- `UsedKB`: Used memory, KB (`sysExtMemoryUsed`).
- `FreeKB`: Free memory, KB (`sysExtMemoryFree`).
- `UsedPct`: Used memory as a percentage of the total (`$UsedKB * 100 / $sysExtMemorySize`).

#### Aruba Controller - Storage (1 minute)

Use of each storage filesystem (flash).

From `WLSX-SYSTEMEXT-MIB::wlsxSysExtStorageTable`, one row per `sysExtStorageName`.
Covers the TES built-in template *Aruba WLC - Storage Utilization*: it polls every object that template polls, under its own names.

- `StorageType`: Storage type (1 RAM, 2 flash memory) (`sysExtStorageType`).
- `UsedMB`: Used storage, MB (`sysExtStorageUsed`).
- `UsedPct`: Used storage as a percentage of the filesystem size (`$UsedMB * 100 / $sysExtStorageSize`).

#### Aruba Controller - Fans (1 minute)

Status of each fan.

From `WLSX-SYSTEMEXT-MIB::wlsxSysExtFanTable`.

- `Status`: Fan status (`sysExtFanStatus`).

#### Aruba Controller - Power Supplies (1 minute)

Status of each power supply.

From `WLSX-SYSTEMEXT-MIB::wlsxSysExtPowerSupplyTable`.
Covers the TES built-in template *Aruba WLC - Fan Status*: it polls every object that template polls, under its own names. Despite its name, Aruba WLC - Fan Status polls the power supply status.

- `Status`: Power supply status (`sysExtPowerSupplyStatus`).

`delta(x)` is the change in counter `x` between the last two polls; `rate(x)` is that change divided by the monitor's polling period in seconds (TES's `$pollingSecs`), a per-second rate.

## Expected variations

A table monitor returns no rows when the device lacks the feature or hardware it covers. An empty table does not necessarily indicate a collection failure: first confirm that the feature is present and configured on the device.

- Access point, radio and ESSID tables depend on the controller having access points and WLANs; a controller can keep access point records while reporting no access points up.
- Authentication server rows depend on configured authentication services; HA profile and heartbeat rows on a redundancy configuration (empty heartbeat data is expected when HA is disabled).
- Licence date and flag formats can vary by ArubaOS release and licence type. Current ArubaOS MIB objects say conductor where older ones said master.
- Rows indexed by a text value, such as the ESSID, can show the index as OID digits where no label column is polled.

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
4. Confirm that SNMP is enabled on the controller and that its SNMP configuration permits the ThirdEye server.
5. Confirm that the MIB modules listed under Requirements are present in ThirdEye.
6. Wait for the applicable collection interval after making a correction.

If scalar metrics work but a table monitor is empty, confirm that the corresponding feature is configured before treating the result as an error.
