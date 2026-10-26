# Avocent ACS Operational and Health and Performance Collections

Last updated: 2026-10-24

Tested against: Not yet validated against a device.

These monitor sets are for: Avocent ACS console servers.

The folder holds two sets; assign both to the same devices:

- **Avocent ACS - Operational Collection** (7 monitors) covers what the device does (its role's state, load and services).
- **Avocent ACS - Health and Performance Collection** (1 monitor) covers the device itself (CPU, memory, storage and hardware).

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

- `ACS-MIB`

ThirdEye's built-in MIB store already includes this module, so no MIB import is needed. The MIB files are not included with these collections; to install or update a module, obtain the files as described below and import them with their dependencies.

### Obtaining the Avocent MIBs

Download the MIB files for the device's software release from Vertiv's Avocent support site, and import the modules listed above together with their dependencies.

## Installation

1. In **Inventory**, use **Tools > SNMP System Info** to verify that ThirdEye can query each target device.
2. Go to **Monitors > Sets** and import `Avocent ACS - Operational Collection.3em` and `Avocent ACS - Health and Performance Collection.3em`.
3. Assign **Avocent ACS - Operational Collection** and **Avocent ACS - Health and Performance Collection** to the appropriate devices.
4. Wait at least 1 minute so every monitor has had a collection opportunity, then confirm that current data is present for the applicable monitors.

Automatic assignment to newly discovered devices is disabled in both sets. Review the sets before enabling automatic assignment in your environment.

These host queries match the devices the sets are meant for: `sysObjectID=1.3.6.1.4.1.10418.*`.

## Included monitors

**Avocent ACS - Operational Collection** contains 7 SNMP monitors and **Avocent ACS - Health and Performance Collection** contains 1 SNMP monitor. Metrics carry friendly names; each lists the MIB object it reads or the formula it computes.

### Avocent ACS - Operational Collection

#### Avocent ACS - Serial Ports (1 minute)

Each serial port: its status and profile, the DCD and CTS signal states, and bytes sent and received and framing, parity, break and overrun errors as the console server reports their totals. Rows are identified by port number, labelled by port name.

From `ACS-MIB::acsSerialPortTable`, one row per `acsSerialPortTableName`.

- `Status`: Port status (1 disabled, 2 idle, 3 inUse) (`acsSerialPortTableStatus`).
- `Profile`: Port profile (1 casProfile, 2 tsProfile, 3 dialInProfile, 4 powerProfile) (`acsSerialPortTableProfile`).
- `Dcd`: DCD signal (1 up, 2 down) (`acsSerialPortTableSignalStateDCD`).
- `Cts`: CTS signal (1 up, 2 down) (`acsSerialPortTableSignalStateCTS`).
- `BytesSent`: Bytes sent (typed Integer32, saved as reported) (`acsSerialPortTableTxBytes`).
- `BytesReceived`: Bytes received (typed Integer32, saved as reported) (`acsSerialPortTableRxBytes`).
- `FrameErrors`: Framing errors (typed Integer32, saved as reported) (`acsSerialPortTableFrameError`).
- `ParityErrors`: Parity errors (typed Integer32, saved as reported) (`acsSerialPortTableParityError`).
- `Breaks`: Breaks (typed Integer32, saved as reported) (`acsSerialPortTableBreak`).
- `Overruns`: Overrun errors (typed Integer32, saved as reported) (`acsSerialPortTableOverrun`).

#### Avocent ACS - Session Count (1 minute)

The number of active sessions on the console server.

From `ACS-MIB` scalars.

- `Sessions`: Active sessions (`acsActiveSessionsNumberOfSession`).

#### Avocent ACS - Active Sessions (1 minute)

Each active session: its user and group, type, connection, how long it has run and where it comes from, as the console server reports them as text. Rows are identified by session index.

From `ACS-MIB::acsActiveSessionsTable`.

- `User`: The session's user (`acsActiveSessionsTableUser`).
- `Group`: The user's group (`acsActiveSessionsTableGroup`).
- `Type`: Session type (`acsActiveSessionsTableType`).
- `Connection`: Connection type (`acsActiveSessionsTableConnection`).
- `Duration`: Time of the session, as text (`acsActiveSessionsTableSessionTime`).
- `From`: Where the session comes from (`acsActiveSessionsTableFrom`).

#### Avocent ACS - Managed PDUs (1 minute)

Each power distribution unit (PDU) the console server manages: its current, voltage, power and power factor, alarm state, and number of outlets. Rows are identified by serial port and PDU, labelled by PDU ID.

From `ACS-MIB::acsPowerMgmtPDUTable`, one row per `acsPowerMgmtPDUTablePduId`.

- `CurrentA`: Current, amperes (from tenths of an ampere) (`$acsPowerMgmtPDUTableCurrentValue / 10.0`).
- `VoltageV`: Voltage, volts (`acsPowerMgmtPDUTableVoltageValue`).
- `PowerW`: Power, watts (from tenths of a watt) (`$acsPowerMgmtPDUTablePowerValue / 10.0`).
- `PowerFactor`: Power factor (from hundredths) (`$acsPowerMgmtPDUTablePowerFactorValue / 100.0`).
- `Alarm`: Alarm state (1 normal, 2 blow-fuse, 3 hw-ocp, 4 high-critical, 5 high-warning, 6 low-warning, 7 low-critical) (`acsPowerMgmtPDUTableAlarm`).
- `Outlets`: Number of outlets (`acsPowerMgmtPDUTableNumberOfOutlets`).

#### Avocent ACS - Managed Outlets (1 minute)

Each outlet of a managed PDU: whether it is on. Rows are identified by serial port, PDU and outlet, labelled by outlet name.

From `ACS-MIB::acsPowerMgmtOutletsTable`, one row per `acsPowerMgmtOutletsTableName`.

- `Status`: Outlet status (1 off, 2 on) (`acsPowerMgmtOutletsTableStatus`).

#### Avocent ACS - Outlet Power (1 minute)

Each outlet of a managed PDU: its current, voltage, power, power factor and alarm state. Rows are identified by serial port, PDU and outlet, as in Avocent ACS - Managed Outlets.

From `ACS-MIB::acsPowerMgmtOutElecMonTable`.

- `CurrentA`: Current, amperes (from tenths of an ampere) (`$acsPowerMgmtOutElecMonTableCurrentValue / 10.0`).
- `VoltageV`: Voltage, volts (`acsPowerMgmtOutElecMonTableVoltageValue`).
- `PowerW`: Power, watts (from tenths of a watt) (`$acsPowerMgmtOutElecMonTablePowerValue / 10.0`).
- `PowerFactor`: Power factor (from hundredths) (`$acsPowerMgmtOutElecMonTablePowerFactorValue / 100.0`).
- `Alarm`: Alarm state (1 normal, 2 blow-fuse, 3 hw-ocp, 4 high-critical, 5 high-warning, 6 low-warning, 7 low-critical) (`acsPowerMgmtOutElecMonTableAlarm`).

#### Avocent ACS - Managed PDU Environment (1 minute)

The environment each managed PDU reports from its sensors: each sensor's type, whether a digital sensor is triggered, and the value an analog sensor detects. Rows are identified by serial port, PDU and sensor, labelled by sensor name.

From `ACS-MIB::acsPowerMgmtEnvMonTable`, one row per `acsPowerMgmtEnvMonTableName`.

- `Type`: Sensor type (1 temp-internal, 2 temperature, 3 humidity, 4 air-flow, 5 smoke, 6 dry-concact, 7 water-level, 8 motion, 9 unplugged, 10 unknown) (`acsPowerMgmtEnvMonTableType`).
- `Status`: Digital sensor status (1 normal, 2 triggered, 3 not-applicable) (`acsPowerMgmtEnvMonTableStatus`).
- `Value`: Detected value: tenths of a degree Celsius for temperature, percent for humidity, air flow units for air flow (saved as reported) (`acsPowerMgmtEnvMonTableValue`).

### Avocent ACS - Health and Performance Collection

#### Avocent ACS - Power Supplies (1 minute)

State of the console server's two power supplies.

From `ACS-MIB` scalars.

- `PowerSupplies`: Number of power supplies (`acsPowerSupplyNumber`).
- `PowerSupply1`: State of the first power supply, as the MIB's PowerSupplyState values (`acsPowerSupplyStatePw1`).
- `PowerSupply2`: State of the second power supply, as the MIB's PowerSupplyState values (`acsPowerSupplyStatePw2`).

`delta(x)` is the change in counter `x` between the last two polls; `rate(x)` is that change divided by the monitor's polling period in seconds (TES's `$pollingSecs`), a per-second rate.

## Expected variations

A table monitor returns no rows when the device lacks the feature or hardware it covers. An empty table does not necessarily indicate a collection failure: first confirm that the feature is present and configured on the device.

- The PDU, outlet and PDU environment monitors return rows only for PDUs the console server manages over a serial port; Active Sessions returns a row per session, so it can have none.
- Rows of the PDU, outlet and sensor tables are identified by index values (serial port, PDU and outlet or sensor), which ThirdEye can show as OID digits.

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
