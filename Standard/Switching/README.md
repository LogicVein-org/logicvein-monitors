# Standard Switching Operational Collection

Last updated: 2026-10-30

Tested against: Not yet validated against a device.

This monitor set is for: Any switch: spanning tree, VLANs, forwarding database, link aggregation and LLDP statistics, from the standard MIBs.

The collection is a starting point. It defines no alert policies or thresholds.

## Requirements

Before importing the monitor set, confirm that:

- The device has been added to the ThirdEye inventory.
- ThirdEye can reach the device's SNMP agent, normally over UDP port 161.
- A working SNMP credential has been assigned to the device in ThirdEye. SNMPv3 with authentication and privacy is recommended.
- The MIB modules listed below are loaded in ThirdEye.

### MIB modules

The collection uses these modules:

- `BRIDGE-MIB`
- `IEEE8023-LAG-MIB`
- `LLDP-MIB`
- `Q-BRIDGE-MIB`

ThirdEye's built-in MIB store already includes all of these modules, so no MIB import is needed. The MIB files are not included with this collection; to install or update a module, obtain the files as described below and import them with their dependencies.

### Obtaining the standard MIBs

`BRIDGE-MIB` ([RFC 4188](https://www.rfc-editor.org/rfc/rfc4188)) and `Q-BRIDGE-MIB` ([RFC 4363](https://www.rfc-editor.org/rfc/rfc4363)) are IETF standard modules, published by the RFC Editor. `IEEE8023-LAG-MIB` (IEEE 802.1AX) and `LLDP-MIB` (IEEE 802.1AB-2005) are IEEE modules, published by the IEEE 802.1 working group.

## Installation

1. In **Inventory**, use **Tools > SNMP System Info** to verify that ThirdEye can query each target device.
2. Go to **Monitors > Sets** and import `Standard Switching - Operational Collection.3em`.
3. Assign **Standard Switching - Operational Collection** to the appropriate devices.
4. Wait at least 5 minutes so every monitor has had a collection opportunity, then confirm that current data is present for the applicable monitors.

Automatic assignment to newly discovered devices is disabled in the set. Review the set before enabling automatic assignment in your environment.

## Included monitors

The set contains 6 SNMP monitors. Metrics carry friendly names; each lists the MIB object it reads or the formula it computes.

### Switching - Spanning Tree (5 minutes)

Spanning tree topology changes, time since the last change, and this bridge's root, root port, root cost and priority. BRIDGE-MIB describes a single spanning tree: on a switch running one per VLAN or per MST instance, which instance these scalars report is device-specific and unverified.

From `BRIDGE-MIB` scalars.

- `TopologyChanges`: Spanning tree topology changes since the previous poll (`delta(dot1dStpTopChanges)`).
- `TopologyChangesPerSec`: Spanning tree topology changes, per second (`rate(dot1dStpTopChanges)`).
- `SinceTopologyChange`: Time since the last topology change, hundredths of a second (TimeTicks, saved raw) (`dot1dStpTimeSinceTopologyChange`).
- `DesignatedRoot`: Bridge ID of the root of the spanning tree (`dot1dStpDesignatedRoot`).
- `RootPort`: Port number of the port with the lowest-cost path toward the root (`dot1dStpRootPort`).
- `RootCost`: Path cost to the root (`dot1dStpRootCost`).
- `Priority`: This bridge's priority (`dot1dStpPriority`).

### Switching - Spanning Tree Ports (5 minutes)

Each bridge port's spanning tree state, whether spanning tree is enabled on it, its path cost, the designated root, bridge and cost it sees, and its transitions to forwarding. Rows are identified by bridge port number, which is not necessarily the ifIndex.

From `BRIDGE-MIB::dot1dStpPortTable`.

- `State`: Port state (1 disabled, 2 blocking, 3 listening, 4 learning, 5 forwarding, 6 broken) (`dot1dStpPortState`).
- `Enabled`: Spanning tree on the port (1 enabled, 2 disabled) (`dot1dStpPortEnable`).
- `PathCost`: The port's contribution to the path cost toward the root (`dot1dStpPortPathCost`).
- `DesignatedRoot`: Bridge ID of the root, as the port's received configuration gives it (`dot1dStpPortDesignatedRoot`).
- `DesignatedBridge`: Bridge ID of the designated bridge for the port's segment (`dot1dStpPortDesignatedBridge`).
- `DesignatedCost`: Path cost of the designated port of the segment (`dot1dStpPortDesignatedCost`).
- `ForwardTransitions`: Transitions from learning to forwarding since the previous poll (`delta(dot1dStpPortForwardTransitions)`).
- `ForwardTransitionsPerSec`: Transitions from learning to forwarding, per second (`rate(dot1dStpPortForwardTransitions)`).

### Switching - VLANs (5 minutes)

The number of VLANs the switch has, and VLANs deleted, from Q-BRIDGE-MIB's device-wide counts.

From `Q-BRIDGE-MIB` scalars.

- `Vlans`: VLANs currently configured (`dot1qNumVlans`).
- `MaxVlans`: Maximum number of VLANs the device supports (`dot1qMaxSupportedVlans`).
- `VlanDeletes`: VLAN entries deleted since the previous poll (`delta(dot1qVlanNumDeletes)`).
- `VlanDeletesPerSec`: VLAN entries deleted, per second (`rate(dot1qVlanNumDeletes)`).

### Switching - Forwarding Database (5 minutes)

Learned MAC addresses in each filtering database. Rows are identified by filtering database ID; with independent VLAN learning each VLAN has its own.

From `Q-BRIDGE-MIB::dot1qFdbTable`.

- `DynamicEntries`: Dynamic (learned) entries in the filtering database now; the MIB types this count Counter32, so it is saved as reported (`dot1qFdbDynamicCount`).

### Switching - Link Aggregation Ports (5 minutes)

Each port's link aggregation (LACP) state: the aggregator it selected and the one it is attached to, whether it can aggregate, and LACP protocol data units in each direction and errors, as counts and rates. Rows are identified by the port's ifIndex.

From `IEEE8023-LAG-MIB::dot3adAggPortTable`.

- `SelectedAggregator`: ifIndex of the aggregator the port has selected (0 for none) (`dot3adAggPortSelectedAggID`).
- `AttachedAggregator`: ifIndex of the aggregator the port is attached to (0 when it is not attached) (`dot3adAggPortAttachedAggID`).
- `Aggregatable`: Whether the port can aggregate with others (TruthValue: 1 true, 2 false) (`dot3adAggPortAggregateOrIndividual`).
- `LacpdusIn`: LACPDUs received since the previous poll (`delta(dot3adAggPortStatsLACPDUsRx)`).
- `LacpdusInPerSec`: LACPDUs received, per second (`rate(dot3adAggPortStatsLACPDUsRx)`).
- `LacpdusOut`: LACPDUs sent since the previous poll (`delta(dot3adAggPortStatsLACPDUsTx)`).
- `LacpdusOutPerSec`: LACPDUs sent, per second (`rate(dot3adAggPortStatsLACPDUsTx)`).
- `IllegalIn`: Frames received that carry the slow protocols type but are badly formed since the previous poll (`delta(dot3adAggPortStatsIllegalRx)`).
- `IllegalInPerSec`: Frames received that carry the slow protocols type but are badly formed, per second (`rate(dot3adAggPortStatsIllegalRx)`).
- `UnknownIn`: Slow protocol frames received with an unknown subtype since the previous poll (`delta(dot3adAggPortStatsUnknownRx)`).
- `UnknownInPerSec`: Slow protocol frames received with an unknown subtype, per second (`rate(dot3adAggPortStatsUnknownRx)`).

### Switching - LLDP Statistics (5 minutes)

LLDP neighbour table changes, as counts and rates: neighbours added, removed, dropped for lack of space and aged out, and the time of the last change.

From `LLDP-MIB` scalars.

- `LastChange`: sysUpTime when the neighbour table last changed, hundredths of a second (TimeStamp, saved raw) (`lldpStatsRemTablesLastChangeTime`).
- `Inserts`: Neighbours added since the previous poll (`delta(lldpStatsRemTablesInserts)`).
- `InsertsPerSec`: Neighbours added, per second (`rate(lldpStatsRemTablesInserts)`).
- `Deletes`: Neighbours removed since the previous poll (`delta(lldpStatsRemTablesDeletes)`).
- `DeletesPerSec`: Neighbours removed, per second (`rate(lldpStatsRemTablesDeletes)`).
- `Drops`: Neighbours dropped for lack of space since the previous poll (`delta(lldpStatsRemTablesDrops)`).
- `DropsPerSec`: Neighbours dropped for lack of space, per second (`rate(lldpStatsRemTablesDrops)`).
- `Ageouts`: Neighbours aged out since the previous poll (`delta(lldpStatsRemTablesAgeouts)`).
- `AgeoutsPerSec`: Neighbours aged out, per second (`rate(lldpStatsRemTablesAgeouts)`).

`delta(x)` is the change in counter `x` between the last two polls; `rate(x)` is that change divided by the monitor's polling period in seconds (TES's `$pollingSecs`), a per-second rate.

## Expected variations

A table monitor returns no rows when the device lacks the feature or hardware it covers. An empty table does not necessarily indicate a collection failure: first confirm that the feature is present and configured on the device.

- Assign it by hand to switches, beside Network Traffic and Errors and the switch's vendor collections.
- Which standard MIBs a device answers, and how fully, is unverified per vendor: a device that does not implement a table returns nothing for its monitor.
- Do not assign it to a device that takes a Cisco collection polling the same MIBs (Cisco IOS Switch), or those objects are polled twice.
- Switching - Spanning Tree reports BRIDGE-MIB's single spanning tree; on a switch running one per VLAN or per MST instance, which instance it reports is device-specific. Spanning Tree Ports rows are bridge port numbers, which are not necessarily ifIndex values.
- The pack's Switching - LLDP Neighbors monitor is not in this collection: its table is indexed by a TimeFilter time mark (RFC 2021), under which a walk can return each neighbour many times. Import it by hand where wanted, and check its rows.

## Validation

To run the **SNMP System Info** tool, open **Inventory**, select the target device, open **Tools**, and select **SNMP System Info**. A successful test returns basic system information, including the system description and uptime, rather than an SNMP timeout or authentication error.

After installation:

1. Run **SNMP System Info** for every assigned device and confirm that it succeeds.
2. Confirm that the 5-minute monitors have current samples.
3. Verify that each applicable table monitor returns the expected rows.
4. Compare values with the device's own interface or CLI before creating alert thresholds.

## Limitations

- The set performs collection only; it does not install alert policies, notification destinations, or incident workflows.
- Counts are changes between polls and rates are per second; alert on those rather than on raw lifetime counters.
- The set contains no SNMP usernames, secrets, device addresses or other environment-specific values.

## Troubleshooting

If no data is collected:

1. Run **Inventory > Tools > SNMP System Info** against the device.
2. Confirm network reachability from ThirdEye to UDP port 161 on the device.
3. Verify the device's SNMP configuration and the credential assigned in ThirdEye.
4. Confirm that the MIB modules listed under Requirements are present in ThirdEye.
5. Wait for the applicable collection interval after making a correction.

If scalar metrics work but a table monitor is empty, confirm that the corresponding feature is configured before treating the result as an error.
