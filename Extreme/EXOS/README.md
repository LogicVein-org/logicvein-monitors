# ExtremeXOS Switch Operational and Health and Performance Collections

Last updated: 2026-10-30

Tested against: Not yet validated against a device.

These monitor sets are for: Extreme Networks switches on ExtremeXOS (Switch Engine).

The folder holds two sets; assign both to the same devices:

- **ExtremeXOS Switch - Operational Collection** (6 monitors) covers what the device does (its role's state, load and services).
- **ExtremeXOS Switch - Health and Performance Collection** (5 monitors) covers the device itself (CPU, memory, storage and hardware).

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

- `EXTREME-EAPS-MIB`
- `EXTREME-ESRP-MIB`
- `EXTREME-POE-MIB`
- `EXTREME-STACKING-MIB`
- `EXTREME-STP-EXTENSIONS-MIB`
- `EXTREME-SYSTEM-MIB`
- `EXTREME-VLAN-MIB`
- `IF-MIB`

ThirdEye's built-in MIB store already includes all of these modules, so no MIB import is needed. The MIB files are not included with these collections; to install or update a module, obtain the files as described below and import them with their dependencies.

### Obtaining the Extreme Networks MIBs

Download the MIB files for the device's software release from Extreme Networks' support site, and import the modules listed above together with their dependencies.

## Installation

1. In **Inventory**, use **Tools > SNMP System Info** to verify that ThirdEye can query each target device.
2. Go to **Monitors > Sets** and import `ExtremeXOS Switch - Operational Collection.3em` and `ExtremeXOS Switch - Health and Performance Collection.3em`.
3. Assign **ExtremeXOS Switch - Operational Collection** and **ExtremeXOS Switch - Health and Performance Collection** to the appropriate devices.
4. Wait at least 5 minutes so every monitor has had a collection opportunity, then confirm that current data is present for the applicable monitors.

Automatic assignment to newly discovered devices is disabled in both sets. Review the sets before enabling automatic assignment in your environment.

These host queries match the devices the sets are meant for: `sysObjectID=1.3.6.1.4.1.1916.*`.

## Included monitors

**ExtremeXOS Switch - Operational Collection** contains 6 SNMP monitors and **ExtremeXOS Switch - Health and Performance Collection** contains 5 SNMP monitors. Metrics carry friendly names; each lists the MIB object it reads or the formula it computes.

### ExtremeXOS Switch - Operational Collection

#### ExtremeXOS - EAPS Domains (5 minutes)

Each Ethernet Automatic Protection Switching (EAPS) ring domain's mode and state, labelled by the domain name. A master in complete state has its ring closed and protected; failed means the ring is broken.

From `EXTREME-EAPS-MIB::extremeEapsTable`, one row per `extremeEapsName`.

- `Mode`: This switch's role in the domain (0 invalid, 1 master, 2 transit) (`extremeEapsMode`).
- `State`: EAPS state (0 idle, 1 complete, 2 failed, 3 linksup, 4 linkdown, 5 preforwarding, 6 init; an older copy of the MIB adds 7 precomplete and 8 preinit, on a master) (`extremeEapsState`).

#### ExtremeXOS - ESRP Domains (5 minutes)

Each Extreme Standby Router Protocol (ESRP) domain's redundancy state and priority (the ESRP group is part of the row key, not a column the agent returns), its active and tracked ports, tracked routes and pings, and transitions to master and slave. Rows are identified by the VLAN's ifIndex and the ESRP group.

From `EXTREME-ESRP-MIB::extremeEsrpTable`.

- `State`: ESRP state (1 neutral, 2 master, 3 slave) (`extremeEsrpState`).
- `Priority`: The priority this switch advertises for the master election (`extremeEsrpPriority`).
- `ActivePorts`: Physical ports in the VLAN (`extremeEsrpActivePorts`).
- `TrackedActivePorts`: Ports in the tracked VLANs (`extremeEsrpTrackedActivePorts`).
- `TrackedRoutes`: IP routes tracked (`extremeEsrpTrackedIpRoutes`).
- `TrackedPings`: Addresses ping-tracked (`extremeEsrpTrackedPings`).
- `TransitionsToMaster`: Transitions to master in total (typed INTEGER, saved as reported) (`extremeEsrpNumTransitionsToMaster`).
- `TransitionsToSlave`: Transitions to slave in total (typed INTEGER, saved as reported) (`extremeEsrpNumTransitionsToSlave`).

#### ExtremeXOS - STP Domains (5 minutes)

Each spanning tree domain (STPD): whether STP and RSTP are enabled, its tag and ports, the root bridge, root port and root cost, and topology changes as a rate and as a count since the previous poll, labelled by the domain name.

From `EXTREME-STP-EXTENSIONS-MIB::extremeStpDomainTable`, one row per `extremeStpDomainStpdName`.

- `StpEnabled`: Whether STP is enabled (TruthValue: 1 true, 2 false) (`extremeStpDomainStpEnabled`).
- `RstpEnabled`: Whether Rapid STP is enabled (TruthValue: 1 true, 2 false) (`extremeStpDomainRstpEnabled`).
- `Tag`: The domain's 802.1Q tag (`extremeStpDomainStpdTag`).
- `Ports`: Physical ports in the domain (`extremeStpDomainNumPorts`).
- `DesignatedRoot`: Bridge ID of the domain's root (`extremeStpDomainDesignatedRoot`).
- `RootPort`: ifIndex of the root port (`extremeStpDomainRootPortIfIndex`).
- `RootCost`: Path cost to the root (`extremeStpDomainRootCost`).
- `TopologyChanges`: Topology changes since the previous poll (`delta(extremeStpDomainTopChanges)`).
- `TopologyChangesPerSec`: Topology changes, per second (`rate(extremeStpDomainTopChanges)`).
- `SinceTopologyChange`: Time since the last topology change, hundredths of a second (TimeTicks, saved raw) (`extremeStpDomainTimeSinceTopologyChange`).

#### ExtremeXOS - VLANs (5 minutes)

Each VLAN's tag, type and administrative state, and whether it ignores STP, labelled by the VLAN's description.

From `EXTREME-VLAN-MIB::extremeVlanIfTable`, one row per `extremeVlanIfDescr`.

- `VlanId`: The VLAN ID (`extremeVlanIfVlanId`).
- `Type`: VLAN interface type, as the switch reports it (ExtremeVlanType) (`extremeVlanIfType`).
- `AdminEnabled`: Whether the VLAN is administratively enabled (TruthValue: 1 true, 2 false) (`extremeVlanIfAdminStatus`).
- `IgnoresStp`: Whether the VLAN's ports ignore STP (TruthValue: 1 true, 2 false) (`extremeVlanIfIgnoreStpFlag`).

#### ExtremeXOS - PoE Slots (5 minutes)

Power over Ethernet (PoE) on each slot as Extreme reports it beyond the standard tables: the PoE system's status, whether inline power is enabled, power allocated, reserved and measured, the most available, the slot's capacity, and which power supply is active.

From `EXTREME-POE-MIB::extremePethPseSlotTable`.

- `Status`: PoE system status (1 initializing, 2 operational, 3 downloadFail, 4 calibrationRequired, 5 invalidFirmware, 6 mismatchVersion, 7 updating, 8 invalidDevice, 9 notOperational, 10 other) (`extremePethSlotPoeStatus`).
- `AdminEnabled`: Whether the slot provides inline power (1 enable, 2 disable) (`extremePethSlotAdminEnable`).
- `AllocatedMw`: Power allocated to PoE devices, milliwatts as TES's copy of the MIB gives it (an older copy says watts) (`extremePethSlotConsumptionPower`).
- `ReservedMw`: Reserved power consumed, milliwatts (`extremePethSlotReservedConsumptionPower`).
- `MeasuredPower`: Power measured for all PoE devices: TES's copy of the MIB gives no unit, an older copy milliwatts (`extremePethSlotMeasuredPower`).
- `MaxAvailableW`: Most power available to the slot, watts (`extremePethSlotMaxAvailPower`).
- `CapacityW`: The slot's hardware capacity, watts (`extremePethSlotMaxCapacity`).
- `ActivePsu`: The inline power supply in use (1 none, 2 internal, 3 external) (`extremePethSlotPSUActive`).

#### ExtremeXOS - PoE Ports (5 minutes)

Power over Ethernet (PoE) on each port as Extreme reports it beyond the standard tables: measured power, the operator's power limit and reserved budget, and the violation precedence. Rows are identified by PSE group and port.

From `EXTREME-POE-MIB::extremePethPsePortTable`.

- `MeasuredMw`: Power the port consumes, milliwatts (`extremePethPortMeasuredPower`).
- `OperatorLimit`: The configured maximum power for the port (milliwatts in an older copy of the MIB; TES's gives no unit) (`extremePethPortOperatorLimit`).
- `ReservedBudget`: The port's reserved power budget: 0, or 3000 to 20000 as TES's copy of the MIB gives it (0 to 30000 milliwatts in an older copy) (`extremePethPortReservedBudget`).
- `ViolationPrecedence`: What a power violation is judged against (1 advertisedClass, 2 operatorLimit, 3 maxAdvertisedOperator, 4 none) (`extremePethPortViolationPrecedence`).

### ExtremeXOS Switch - Health and Performance Collection

#### ExtremeXOS - Temperature (5 minutes)

Temperature inside the switch enclosure, and whether the over-temperature alarm is raised.

From `EXTREME-SYSTEM-MIB` scalars.

- `TemperatureC`: Temperature inside the enclosure, degrees Celsius (`extremeCurrentTemperature`).
- `OverTemperatureAlarm`: Over-temperature alarm (TruthValue: true(1), false(2)) (`extremeOverTemperatureAlarm`).

#### ExtremeXOS - Fans (5 minutes)

Whether each cooling fan is operational.

From `EXTREME-SYSTEM-MIB::extremeFanStatusTable`.

- `Operational`: Fan operational (TruthValue: true(1), false(2)) (`extremeFanOperational`).

#### ExtremeXOS - Power Supplies (5 minutes)

Status of each power supply.

From `EXTREME-SYSTEM-MIB::extremePowerSupplyTable`.

- `Status`: Power supply status (notPresent, presentOK, presentNotOK) (`extremePowerSupplyStatus`).

#### ExtremeXOS - Stack Members (5 minutes)

Each SummitStack member's operational state, role, priority, stacking state and running image version. Rows are identified by slot.

From `EXTREME-STACKING-MIB::extremeStackMemberTable`.

- `OperStatus`: Operational state (1 up, 2 down, 3 mismatch) (`extremeStackMemberOperStatus`).
- `Role`: Role in the stack (1 master, 2 slave) (`extremeStackMemberRole`).
- `Priority`: Stack priority for the master election (`extremeStackMemberStackPriority`).
- `StackingEnabled`: Whether stacking is enabled for the member (1 enable, 2 disable) (`extremeStackMemberStackStatus`).
- `ImageVersion`: The image version the member runs (`extremeStackMemberCurImageVersion`).

#### ExtremeXOS - Stack Ports (5 minutes)

Each stacking port's link state and speed, and the MAC address on its far end, labelled by ifName.

From `EXTREME-STACKING-MIB::extremeStackingPortTable`, one row per `ifName`, also saved as `InterfaceName` for trigger messages.

- `LinkStatus`: Stacking link state (1 up, 2 down) (`extremeStackingPortLinkStatus`).
- `SpeedBps`: Link speed, bits per second; 0 when the link is down (`extremeStackingPortLinkSpeed`).
- `RemoteMac`: MAC address of the system on the far end; all zeros when the link is down (`extremeStackingPortRemoteMac`).

`delta(x)` is the change in counter `x` between the last two polls; `rate(x)` is that change divided by the monitor's polling period in seconds (TES's `$pollingSecs`), a per-second rate.

## Expected variations

A table monitor returns no rows when the device lacks the feature or hardware it covers. An empty table does not necessarily indicate a collection failure: first confirm that the feature is present and configured on the device.

- The EAPS, ESRP, STP domain and PoE monitors return no rows on a switch where the feature is not configured, and the PoE monitors on a switch without PoE.
- ESRP's transition counts are typed INTEGER though described as totals, so they are saved as reported rather than as rates.
- ExtremeXOS - PoE Slots and ExtremeXOS - PoE Ports add what Extreme's PoE MIB reports beyond the standard POWER-ETHERNET-MIB tables.

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
