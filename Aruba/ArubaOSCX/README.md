# ArubaOS-CX Switch Operational and Health and Performance Collections

Last updated: 2026-10-24

Tested against: Not yet validated against a device.

These monitor sets are for: Aruba CX switches (6000, 6100, 6200, 6300, 6400, 8xxx series).

The folder holds two sets; assign both to the same devices:

- **ArubaOS-CX Switch - Operational Collection** (5 monitors) covers what the device does (its role's state, load and services).
- **ArubaOS-CX Switch - Health and Performance Collection** (11 monitors) covers the device itself (CPU, memory, storage and hardware).

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

- `ARUBAWIRED-FAN-MIB`
- `ARUBAWIRED-POE-MIB`
- `ARUBAWIRED-POWERSUPPLY-MIB`
- `ARUBAWIRED-RPVST-MIB`
- `ARUBAWIRED-SYSTEMINFO-MIB`
- `ARUBAWIRED-TEMPSENSOR-MIB`
- `ARUBAWIRED-VSF-MIB`
- `ARUBAWIRED-VSFv2-MIB`
- `ARUBAWIRED-VSX-MIB`

ThirdEye's built-in MIB store already includes all of these modules, so no MIB import is needed. The MIB files are not included with these collections; to install or update a module, obtain the files as described below and import them with their dependencies.

### Obtaining the Aruba MIBs

Download the MIB files for the device's software release from the HPE Aruba Networking Support Portal, and import the modules listed above together with their dependencies.

## Installation

1. In **Inventory**, use **Tools > SNMP System Info** to verify that ThirdEye can query each target device.
2. Go to **Monitors > Sets** and import `ArubaOS-CX Switch - Operational Collection.3em` and `ArubaOS-CX Switch - Health and Performance Collection.3em`.
3. Assign **ArubaOS-CX Switch - Operational Collection** and **ArubaOS-CX Switch - Health and Performance Collection** to the appropriate devices.
4. Wait at least 1 minute so every monitor has had a collection opportunity, then confirm that current data is present for the applicable monitors.

Automatic assignment to newly discovered devices is disabled in both sets. Review the sets before enabling automatic assignment in your environment.

These host queries match the devices the sets are meant for: `sysObjectID=1.3.6.1.4.1.47196.*`.

## Included monitors

**ArubaOS-CX Switch - Operational Collection** contains 5 SNMP monitors and **ArubaOS-CX Switch - Health and Performance Collection** contains 11 SNMP monitors. Metrics carry friendly names; each lists the MIB object it reads or the formula it computes.

### ArubaOS-CX Switch - Operational Collection

#### ArubaOS-CX - VSX (1 minute)

Virtual Switching Extension (VSX) between a pair of switches: this switch's role, whether configuration sync is enabled, the inter-switch link (ISL) state, the keepalive state, keepalive timeouts and dropped packets, and the most recent keepalive failure.

From `ARUBAWIRED-VSX-MIB` scalars.

- `Role`: VSX role (1 primary, 2 secondary, 3 notConfigured) (`arubaWiredVsxDeviceRole`).
- `ConfigSync`: Whether configuration sync is enabled (1 enabled, 2 disabled) (`arubaWiredVsxConfigSync`).
- `IslState`: ISL state (1 init, 2 outSync, 3 inSync) (`arubaWiredVsxIslOperState`).
- `KeepAliveState`: Keepalive state (1 init, 2 configured, 3 inSyncEstablished, 4 outofSyncEstablished, 5 initEstablished, 6 failed, 7 stopped) (`arubaWiredVsxKeepAliveOperState`).
- `KeepAliveTimeouts`: Times keepalive failed on timer expiry (typed Integer32, saved as reported) (`arubaWiredVsxKeepAliveTimeoutCount`).
- `KeepAliveDrops`: Keepalive packets dropped (typed Integer32, saved as reported) (`arubaWiredVsxKeepAlivePacketsDrop`).
- `LastKeepAliveFailure`: The most recent time the keepalive handshake failed, 0 for never (TimeTicks, saved raw) (`arubaWiredVsxKeepAliveLastFailedTime`).
- `SystemId`: The system identifier derived from both switches (`arubaWiredVsxDeviceOperSystemID`).

#### ArubaOS-CX - RPVST VLANs (1 minute)

Rapid per-VLAN spanning tree for each VLAN: whether it is enabled, this bridge's priority, the root bridge's priority and MAC address, the root port and path cost, root changes and topology changes as rates and as counts since the previous poll, and the time since the last topology change. Rows are identified by VLAN ID.

From `ARUBAWIRED-RPVST-MIB::arubaWiredRpvstVlanTable`.

- `AdminStatus`: Whether RPVST is enabled on the VLAN (1 enabled, 2 disabled) (`arubaWiredRpvstVlanRpvstAdminStatus`).
- `Priority`: This bridge's priority (`arubaWiredRpvstVlanPriority`).
- `RootPriority`: The root bridge's priority (`arubaWiredRpvstVlanRootPriority`).
- `RootMac`: The root bridge's MAC address (`arubaWiredRpvstVlanRootMacAddress`).
- `RootPort`: ifIndex of the root port (`arubaWiredRpvstVlanRootPort`).
- `RootCost`: Path cost to the root (`arubaWiredRpvstVlanRootPathCost`).
- `RootChanges`: Root bridge changes since the previous poll (`delta(arubaWiredRpvstVlanRootChangeCounter)`).
- `RootChangesPerSec`: Root bridge changes, per second (`rate(arubaWiredRpvstVlanRootChangeCounter)`).
- `TopologyChanges`: Topology changes since the previous poll (`delta(arubaWiredRpvstVlanTopoChangeCount)`).
- `TopologyChangesPerSec`: Topology changes, per second (`rate(arubaWiredRpvstVlanTopoChangeCount)`).
- `SinceTopologyChange`: Time since the last topology change, hundredths of a second (TimeTicks, saved raw) (`arubaWiredRpvstVlanTimeSinceLastTopoChange`).

#### ArubaOS-CX - RPVST Ports (1 minute)

Each port's rapid per-VLAN spanning tree role and state in each VLAN, and why it is inconsistent if it is. Rows are identified by VLAN ID and the port's ifIndex.

From `ARUBAWIRED-RPVST-MIB::arubaWiredRpvstPortVlanTable`.

- `Role`: Port role (1 root, 2 alternate, 3 designated, 4 backup, 5 master, 6 disabled) (`arubaWiredRpvstPortVlanRole`).
- `State`: Port state (1 disabled, 2 blocking, 3 listening, 4 learning, 5 forwarding, 6 broken, 7 bpduError, 8 loopInconsistent, 9 pvidInconsistent, 10 rootGuard) (`arubaWiredRpvstPortVlanState`).
- `Inconsistency`: Why the port is inconsistent (0 invalid, 1 rootProtected, 2 loopProtected, 3 inconsistentPvidProtected) (`arubaWiredRpvstPortVlanInconsistencyReason`).

#### ArubaOS-CX - PoE Ports (1 minute)

Power over Ethernet (PoE) on each port as ArubaOS-CX reports it beyond the standard tables: its operational status, power drawn, averaged and at peak, power reserved, current and voltage, and the class the switch assigned. Rows are identified by PSE group and port.

From `ARUBAWIRED-POE-MIB::arubaWiredPoePethPsePortTable`.

- `OperStatus`: Port PoE status (1 deny, 2 off, 3 on) (`arubaWiredPoePethPsePortOperStatus`).
- `DrawnMw`: Power the powered device draws, milliwatts (`arubaWiredPoePethPsePortPowerDrawn`).
- `AverageMw`: Average power drawn, milliwatts (`arubaWiredPoePethPsePortAveragePower`).
- `PeakMw`: Peak power supplied, milliwatts (`arubaWiredPoePethPsePortPeakPower`).
- `ReservedMw`: Power reserved for the powered device, milliwatts (`arubaWiredPoePethPsePortReservedPower`).
- `CurrentMa`: Current supplied, milliamperes (`arubaWiredPoePethPsePortCurrent`).
- `VoltageV`: Voltage supplied, volts, from the MIB's deciVolts (its description also says tens of volts) (`$arubaWiredPoePethPsePortVoltage / 10.0`).
- `AssignedClass`: The class the switch assigned (1 class0, 2 class1, 3 class2, 4 class3, 5 class4, 6 class5, 7 class6) (`arubaWiredPoePethPsePortPseAssignedClass`).

#### ArubaOS-CX - PoE Power (1 minute)

Power over Ethernet (PoE) power per PSE group as ArubaOS-CX reports it beyond the standard tables: power available for PoE, power available after a single power supply failure, and power reserved for redundancy. Chassis subsystems only.

From `ARUBAWIRED-POE-MIB::arubaWiredPoePethMainPseTable`.

- `AvailableW`: Power available for PoE delivery, watts (`arubaWiredPoePethMainPseReservedPower`).
- `FailoverW`: Power available for PoE after a single power supply failure, watts (`arubaWiredPoePethMainPseFailoverPower`).
- `RedundantW`: Power reserved by the power supplies for redundancy, watts (`arubaWiredPoePethMainPseRedundantPower`).

### ArubaOS-CX Switch - Health and Performance Collection

#### ArubaOS-CX - System (1 minute)

CPU, memory and storage utilization per management module or stack member.

From `ARUBAWIRED-SYSTEMINFO-MIB::arubaWiredSystemInfoTable`, one row per `arubaWiredSystemInfoModuleName`.

- `CpuPct`: CPU utilization averaged across the module's CPUs, percent (`arubaWiredSystemInfoCpu`).
- `MemoryPct`: Memory usage, percent (`arubaWiredSystemInfoMemory`).
- `StorageNosPct`: Operating system storage partition used, percent (`arubaWiredSystemInfoStorageNos`).
- `StorageLogPct`: Log storage partition used, percent (`arubaWiredSystemInfoStorageLog`).

#### ArubaOS-CX - Temperature (1 minute)

Temperature per sensor and its state, with the historic maximum.

From `ARUBAWIRED-TEMPSENSOR-MIB::arubaWiredTempSensorTable`, one row per `arubaWiredTempSensorName`.

- `Temperature`: Current reading, in the units the switch reports (the MIB does not state them) (`arubaWiredTempSensorTemperature`).
- `MaxTemperature`: Highest reading recorded, same units (`arubaWiredTempSensorMaxTemp`).
- `State`: Sensor state as text (`arubaWiredTempSensorState`).

#### ArubaOS-CX - Fans (1 minute)

State and speed of each fan.

From `ARUBAWIRED-FAN-MIB::arubaWiredFanTable`, one row per `arubaWiredFanName`.

- `State`: Fan state as text (`arubaWiredFanState`).
- `Rpm`: Fan speed, revolutions per minute (`arubaWiredFanRPM`).

#### ArubaOS-CX - Power Supplies (1 minute)

State and load of each power supply.

From `ARUBAWIRED-POWERSUPPLY-MIB::arubaWiredPowerSupplyTable`, one row per `arubaWiredPSUName`.

- `State`: Power supply state as text (`arubaWiredPSUState`).
- `PowerWatts`: Power being supplied now, watts (`arubaWiredPSUInstantaneousPower`).
- `LoadPct`: Power supplied as a percentage of the supply's maximum capacity (`$PowerWatts * 100 / $arubaWiredPSUMaximumPower`).
- `Failures`: Failures since the supply was inserted and the system booted (`arubaWiredPSUNumberFailures`).

#### ArubaOS-CX - VSF Members (1 minute)

Each Virtual Switching Framework (VSF) stack member's role and status, CPU and memory use, and software image. Rows are identified by member ID.

From `ARUBAWIRED-VSF-MIB::arubaWiredVsfMemberTable`.

- `Role`: The member's role in the stack, as the switch reports it (`arubaWiredVsfMemberRole`).
- `Status`: The member's status (not_present, booting, ready and so on, as the switch reports it) (`arubaWiredVsfMemberStatus`).
- `CpuPct`: CPU utilization, percent (`arubaWiredVsfMemberCpuUtil`).
- `MemoryPct`: Memory utilization, percent (`arubaWiredVsfMemberMemoryUtil`).
- `Image`: The software image the member runs (`arubaWiredVsfMemberBootImage`).

#### ArubaOS-CX - VSF Stack (1 minute)

The Virtual Switching Framework (VSF) stack's split state: no_split, fragment_active or fragment_inactive.

From `ARUBAWIRED-VSF-MIB` scalars.

- `SplitState`: The stack's split state (`arubaWiredVsfOperStatus`).

#### ArubaOS-CX - VSF Members (VSFv2) (1 minute)

Each Virtual Switching Framework (VSF) stack member's role and status, CPU and memory use, and software image, from ARUBAWIRED-VSFv2-MIB, which carries the same member objects as ARUBAWIRED-VSF-MIB. Rows are identified by member ID.

From `ARUBAWIRED-VSFv2-MIB::arubaWiredVsfv2MemberTable`.

- `Role`: The member's role in the stack, as the switch reports it (`arubaWiredVsfv2MemberRole`).
- `Status`: The member's status, as the switch reports it (`arubaWiredVsfv2MemberStatus`).
- `CpuPct`: CPU utilization, percent (`arubaWiredVsfv2MemberCpuUtil`).
- `MemoryPct`: Memory utilization, percent (`arubaWiredVsfv2MemberMemoryUtil`).
- `Image`: The software image the member runs (`arubaWiredVsfv2MemberBootImage`).

#### ArubaOS-CX - VSF Stack (VSFv2) (1 minute)

The VSF stack's split state (no_split, fragment_active or fragment_inactive) and its designated secondary member, normally the standby, from ARUBAWIRED-VSFv2-MIB.

From `ARUBAWIRED-VSFv2-MIB` scalars.

- `SplitState`: The stack's split state, as the switch reports it (`arubaWiredVsfv2OperStatus`).
- `Secondary`: The designated secondary member (`arubaWiredVsfv2Secondary`).

#### ArubaOS-CX - VSF Links (1 minute)

Each Virtual Switching Framework (VSF) stack link's state, and the peer member and link at its other end. Rows are identified by member ID and link ID.

From `ARUBAWIRED-VSF-MIB::arubaWiredVsfLinkTable`.

- `State`: The link's state, as the switch reports it (`arubaWiredVsfLinkOperStatus`).
- `PeerMember`: The member ID of the peer switch on the link (`arubaWiredVsfLinkPeerMemberId`).
- `PeerLink`: The link ID of the peer member on the link (`arubaWiredVsfLinkPeerLinkId`).

#### ArubaOS-CX - VSF Links (VSFv2) (1 minute)

Each Virtual Switching Framework (VSF) stack link's state, and the peer member and link at its other end, from ARUBAWIRED-VSFv2-MIB. Rows are identified by member ID and link ID.

From `ARUBAWIRED-VSFv2-MIB::arubaWiredVsfv2LinkTable`.

- `State`: The link's state, as the switch reports it (`arubaWiredVsfv2LinkOperStatus`).
- `PeerMember`: The member ID of the peer switch on the link (`arubaWiredVsfv2LinkPeerMemberId`).
- `PeerLink`: The link ID of the peer member on the link (`arubaWiredVsfv2LinkPeerLinkId`).

#### ArubaOS-CX - VSF Ports (VSFv2) (1 minute)

Each Virtual Switching Framework (VSF) physical port's state and status text, and the system MAC address and product type of the peer switch connected to it, from ARUBAWIRED-VSFv2-MIB. Rows are identified by ifIndex.

From `ARUBAWIRED-VSFv2-MIB::arubaWiredVsfv2PortTable`.

- `State`: The port's state (up, down, error or autojoin in progress) (`arubaWiredVsfv2PortOperStatus`).
- `Status`: Why the port is in that state, as text (for example Peer timed out) (`arubaWiredVsfv2PortStatusStr`).
- `PeerMac`: The peer switch's system MAC address (`arubaWiredVsfv2PortPeerSysMac`).
- `PeerProduct`: The peer switch's product type (`arubaWiredVsfv2PortPeerProductType`).

`delta(x)` is the change in counter `x` between the last two polls; `rate(x)` is that change divided by the monitor's polling period in seconds (TES's `$pollingSecs`), a per-second rate.

## Expected variations

A table monitor returns no rows when the device lacks the feature or hardware it covers. An empty table does not necessarily indicate a collection failure: first confirm that the feature is present and configured on the device.

- The VSX, spanning tree and PoE monitors can return no rows or values on a switch where the feature is not configured or the hardware has no PoE.
- Rows of the RPVST Ports and PoE tables are identified by index values (VLAN ID and ifIndex, PSE group and port), which ThirdEye can show as OID digits.
- A switch that is not a VSF stack member can return no rows for the VSF monitors; which of ARUBAWIRED-VSF-MIB and ARUBAWIRED-VSFv2-MIB a switch answers is unverified.

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
