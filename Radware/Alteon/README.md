# Alteon Application Switch Operational and Health and Performance Collections

Last updated: 2026-10-26

Tested against: Not yet validated against a device.

These monitor sets are for: Alteon and Radware Alteon application switches.

The folder holds two sets; assign both to the same devices:

- **Alteon Application Switch - Operational Collection** (6 monitors) covers what the device does (its role's state, load and services).
- **Alteon Application Switch - Health and Performance Collection** (2 monitors) covers the device itself (CPU, memory, storage and hardware).

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

- `ALTEON-CHEETAH-LAYER4-MIB`
- `ALTEON-CHEETAH-NETWORK-MIB`
- `ALTEON-CHEETAH-SWITCH-MIB`
- `ALTEON-TIGON-SWITCH-MIB`

ThirdEye's built-in MIB store already includes all of these modules, so no MIB import is needed. The MIB files are not included with these collections; to install or update a module, obtain the files as described below and import them with their dependencies.

### Obtaining the Radware MIBs

Download the MIB files for the device's software release from Radware's support portal, and import the modules listed above together with their dependencies.

## Installation

1. In **Inventory**, use **Tools > SNMP System Info** to verify that ThirdEye can query each target device.
2. Go to **Monitors > Sets** and import `Alteon Application Switch - Operational Collection.3em` and `Alteon Application Switch - Health and Performance Collection.3em`.
3. Assign **Alteon Application Switch - Operational Collection** and **Alteon Application Switch - Health and Performance Collection** to the appropriate devices.
4. Wait at least 1 minute so every monitor has had a collection opportunity, then confirm that current data is present for the applicable monitors.

Automatic assignment to newly discovered devices is disabled in both sets. Review the sets before enabling automatic assignment in your environment.

These host queries match the devices the sets are meant for: `sysObjectID=1.3.6.1.4.1.1872.*`.

## Included monitors

**Alteon Application Switch - Operational Collection** contains 6 SNMP monitors and **Alteon Application Switch - Health and Performance Collection** contains 2 SNMP monitors. Metrics carry friendly names; each lists the MIB object it reads or the formula it computes.

### Alteon Application Switch - Operational Collection

#### Alteon - Virtual Servers (1 minute)

Each virtual server's current and peak sessions, new sessions and traffic as rates and as counts since the previous poll, labelled by its address.

From `ALTEON-CHEETAH-LAYER4-MIB::slbStatVServerTable`, one row per `slbStatVServerIpAddress`.

- `Sessions`: Sessions the virtual server is handling now (`slbStatVServerCurrSessions`).
- `PeakSessions`: Most sessions handled at once (typed Counter32; the MIB describes a high-water mark, saved raw) (`slbStatVServerHighestSessions`).
- `NewSessions`: Sessions handled since the previous poll (`delta(slbStatVServerTotalSessions)`).
- `NewSessionsPerSec`: Sessions handled, per second (`rate(slbStatVServerTotalSessions)`).
- `Bytes`: Bytes received and sent since the previous poll (`delta(slbStatVServerHCOctets)`).
- `BytesPerSec`: Bytes received and sent, per second (`rate(slbStatVServerHCOctets)`).

#### Alteon - Virtual Services (1 minute)

The state of each real server for each virtual service, its health check response time and weight, and the virtual and real ports. Rows are identified by virtual server, service and real server numbers.

From `ALTEON-CHEETAH-LAYER4-MIB::slbVirtServicesInfoTable`.

- `State`: The real server's state for the service (1 blocked, 2 running, 3 failed, 4 disabled, 5 slowstart) (`slbVirtServicesInfoState`).
- `ResponseTime`: Health check response time, as the switch reports it (the MIB gives no unit) (`slbVirtServicesInfoResponseTime`).
- `Weight`: The real server's weight (`slbVirtServicesInfoWeight`).
- `VirtualPort`: The service's layer 4 virtual port (`slbVirtServicesInfoVport`).
- `RealPort`: The service's layer 4 real port (`slbVirtServicesInfoRport`).
- `RealServer`: The real server number (`slbVirtServicesInfoRealServIndex`).

#### Alteon - Real Servers (1 minute)

Each real server's state and overflow state, its address and VLAN, and the OSI layer its health is checked at. Rows are identified by real server number.

From `ALTEON-CHEETAH-LAYER4-MIB::slbRealServerInfoTable`.

- `State`: Real server state (2 running, 3 failed, 4 disabled) (`slbRealServerInfoState`).
- `Overflow`: Overflow state (1 overflow, 2 no-overflow) (`slbRealServerInfoOverflow`).
- `Address`: The real server's address (`slbRealServerInfoIpAddr`).
- `Vlan`: The VLAN the real server belongs to (`slbRealServerInfoVlan`).
- `HealthLayer`: The layer the health check verifies (1 layer1, 3 layer3, 4 layer4) (`slbRealServerInfoHealthLayer`).

#### Alteon - Real Server Traffic (1 minute)

Each real server's current and peak sessions, and new sessions, times claimed down and traffic as rates and as counts since the previous poll. Rows are identified by real server number.

From `ALTEON-CHEETAH-LAYER4-MIB::slbStatRServerTable`.

- `Sessions`: Sessions the real server is handling now (`slbStatRServerCurrSessions`).
- `PeakSessions`: Most sessions handled at once (typed Counter32; the MIB describes a high-water mark, saved raw) (`slbStatRServerHighestSessions`).
- `NewSessions`: Sessions handled since the previous poll (`delta(slbStatRServerTotalSessions)`).
- `NewSessionsPerSec`: Sessions handled, per second (`rate(slbStatRServerTotalSessions)`).
- `Failures`: Times the real server was claimed down since the previous poll (`delta(slbStatRServerFailures)`).
- `FailuresPerSec`: Times the real server was claimed down, per second (`rate(slbStatRServerFailures)`).
- `Bytes`: Bytes received and sent since the previous poll (`delta(slbStatRServerHCOctets)`).
- `BytesPerSec`: Bytes received and sent, per second (`rate(slbStatRServerHCOctets)`).

#### Alteon - Groups (1 minute)

Each real server group's current and peak sessions, and new sessions and traffic as rates and as counts since the previous poll. Rows are identified by group number.

From `ALTEON-CHEETAH-LAYER4-MIB::slbStatGroupTable`.

- `Sessions`: Sessions the group is handling now (`slbStatGroupCurrSessions`).
- `PeakSessions`: Most sessions handled at once (typed Counter32; the MIB describes a high-water mark, saved raw) (`slbStatGroupHighestSessions`).
- `NewSessions`: Sessions handled since the previous poll (`delta(slbStatGroupTotalSessions)`).
- `NewSessionsPerSec`: Sessions handled, per second (`rate(slbStatGroupTotalSessions)`).
- `Bytes`: Bytes received and sent since the previous poll (`delta(slbStatGroupHCOctets)`).
- `BytesPerSec`: Bytes received and sent, per second (`rate(slbStatGroupHCOctets)`).

#### Alteon - VRRP Virtual Routers (1 minute)

High availability between Alteon switches: each VRRP virtual router's state, ownership and priority, and whether it carries layer 4 services or proxies. Rows are identified by virtual router number.

From `ALTEON-CHEETAH-NETWORK-MIB::vrrpInfoVirtRtrTable`.

- `State`: Virtual router state (1 init, 2 master, 3 backup, 4 holdoff) (`vrrpInfoVirtRtrState`).
- `Ownership`: Ownership (1 owner, 2 renter) (`vrrpInfoVirtRtrOwnership`).
- `Priority`: The virtual router's priority (`vrrpInfoVirtRtrPriority`).
- `Layer4Services`: Whether it carries layer 4 services (1 yes, 2 no) (`vrrpInfoVirtRtrServer`).
- `Proxy`: Whether it is a proxy router (1 yes, 2 no) (`vrrpInfoVirtRtrProxy`).

### Alteon Application Switch - Health and Performance Collection

#### Alteon - CPU (1 minute)

Management processor utilization over 4 and 64 seconds.

From `ALTEON-CHEETAH-SWITCH-MIB` scalars.

- `Cpu4SecPct`: MP CPU utilization over 4 seconds, percent (`mpCpuStatsUtil4Seconds`).
- `Cpu64SecPct`: MP CPU utilization over 64 seconds, percent (`mpCpuStatsUtil64Seconds`).

#### Alteon - Hardware (1 minute)

Power supply status and the temperature sensors.

From `ALTEON-TIGON-SWITCH-MIB` scalars.

- `PowerSupply`: Internal power supply status (1 ok, 2 bad) (`hwPowerSupplyStatus`).
- `RedundantPowerSupply`: Redundant power supply status (1 ok, 2 bad, 3 not present) (`hwRedundantPSStatus`).
- `Sensor1TempC`: Sensor 1 (rear left) temperature, degrees Celsius (`hwSensor1Temp`).
- `Sensor2TempC`: Sensor 2 temperature, degrees Celsius (`hwSensor2Temp`).
- `Sensor3TempC`: Sensor 3 temperature, degrees Celsius (`hwSensor3Temp`).
- `Sensor4TempC`: Sensor 4 temperature, degrees Celsius (`hwSensor4Temp`).

`delta(x)` is the change in counter `x` between the last two polls; `rate(x)` is that change divided by the monitor's polling period in seconds (TES's `$pollingSecs`), a per-second rate.

## Expected variations

A table monitor returns no rows when the device lacks the feature or hardware it covers. An empty table does not necessarily indicate a collection failure: first confirm that the feature is present and configured on the device.

- The virtual server, real server, group and VRRP monitors return no rows on a switch where the feature is not configured.
- PeakSessions is typed Counter32 but the MIB describes it as the most sessions handled at once, so it is saved as reported, not as a rate.
- Alteon - Virtual Services has one row per real server of each virtual service, identified by virtual server, service and real server numbers, which ThirdEye can show as OID digits.

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
