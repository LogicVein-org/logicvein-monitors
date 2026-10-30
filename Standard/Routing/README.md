# Standard Routing Operational Collection

Last updated: 2026-10-30

Tested against: Not yet validated against a device.

This monitor set is for: Any router or routed switch: BGP peers, OSPF neighbours, VRRP and VRRPv3 virtual routers and route counts, from the standard MIBs.

The collection is a starting point. It defines no alert policies or thresholds.

## Requirements

Before importing the monitor set, confirm that:

- The device has been added to the ThirdEye inventory.
- ThirdEye can reach the device's SNMP agent, normally over UDP port 161.
- A working SNMP credential has been assigned to the device in ThirdEye. SNMPv3 with authentication and privacy is recommended.
- The MIB modules listed below are loaded in ThirdEye.

### MIB modules

The collection uses these modules:

- `CES-BGP-DEFAULTS-MIB`
- `IP-FORWARD-MIB`
- `OSPF-MIB`
- `VRRP-MIB`
- `VRRPV3-MIB`

ThirdEye's built-in MIB store already includes all of these modules, so no MIB import is needed. The MIB files are not included with this collection; to install or update a module, obtain the files as described below and import them with their dependencies.

### Obtaining the standard MIBs

`BGP4-MIB` ([RFC 4273](https://www.rfc-editor.org/rfc/rfc4273)), `OSPF-MIB` ([RFC 4750](https://www.rfc-editor.org/rfc/rfc4750)), `VRRP-MIB` ([RFC 2787](https://www.rfc-editor.org/rfc/rfc2787)), `VRRPV3-MIB` ([RFC 6527](https://www.rfc-editor.org/rfc/rfc6527)) and `IP-FORWARD-MIB` ([RFC 4292](https://www.rfc-editor.org/rfc/rfc4292)) are IETF standard modules, published by the RFC Editor. ThirdEye's built-in MIB store files BGP4-MIB's objects (`1.3.6.1.2.1.15`) under the module name `CES-BGP-DEFAULTS-MIB`, which is why that name appears in the list above.

## Installation

1. In **Inventory**, use **Tools > SNMP System Info** to verify that ThirdEye can query each target device.
2. Go to **Monitors > Sets** and import `Standard Routing - Operational Collection.3em`.
3. Assign **Standard Routing - Operational Collection** to the appropriate devices.
4. Wait at least 5 minutes so every monitor has had a collection opportunity, then confirm that current data is present for the applicable monitors.

Automatic assignment to newly discovered devices is disabled in the set. Review the set before enabling automatic assignment in your environment.

## Included monitors

The set contains 5 SNMP monitors. Metrics carry friendly names; each lists the MIB object it reads or the formula it computes.

### Routing - BGP Peers (5 minutes)

Each BGP peer's session state, administrative status and remote AS, how long the session has been established, session flaps, and updates in each direction. Rows are identified by the peer's address. BGP4-MIB covers IPv4 peers only.

From `BGP4-MIB::bgpPeerTable`.

- `State`: Session state (1 idle, 2 connect, 3 active, 4 opensent, 5 openconfirm, 6 established) (`bgpPeerState`).
- `AdminStatus`: Administrative status (1 stop, 2 start) (`bgpPeerAdminStatus`).
- `RemoteAs`: The peer's autonomous system number (`bgpPeerRemoteAs`).
- `EstablishedSecs`: Seconds the session has been established, or since it was last established (`bgpPeerFsmEstablishedTime`).
- `Transitions`: Times the session entered the established state since the previous poll (`delta(bgpPeerFsmEstablishedTransitions)`).
- `TransitionsPerSec`: Times the session entered the established state, per second (`rate(bgpPeerFsmEstablishedTransitions)`).
- `UpdatesIn`: Updates received since the previous poll (`delta(bgpPeerInUpdates)`).
- `UpdatesInPerSec`: Updates received, per second (`rate(bgpPeerInUpdates)`).
- `UpdatesOut`: Updates sent since the previous poll (`delta(bgpPeerOutUpdates)`).
- `UpdatesOutPerSec`: Updates sent, per second (`rate(bgpPeerOutUpdates)`).
- `SinceUpdateInSecs`: Seconds since the last update was received (`bgpPeerInUpdateElapsedTime`).

### Routing - OSPF Neighbors (5 minutes)

Each OSPF neighbour's adjacency state, router ID and priority, adjacency state changes, and its link-state retransmission queue. Rows are identified by the neighbour's address.

From `OSPF-MIB::ospfNbrTable`.

- `State`: Adjacency state (1 down, 2 attempt, 3 init, 4 twoWay, 5 exchangeStart, 6 exchange, 7 loading, 8 full); 8 full, or 4 twoWay with a non-designated router, is normal (`ospfNbrState`).
- `RouterId`: The neighbour's OSPF router ID (`ospfNbrRtrId`).
- `Priority`: The neighbour's designated-router priority (`ospfNbrPriority`).
- `StateChanges`: Adjacency state changes or errors since the previous poll (`delta(ospfNbrEvents)`).
- `StateChangesPerSec`: Adjacency state changes or errors, per second (`rate(ospfNbrEvents)`).
- `RetransmitQueue`: Link-state advertisements waiting to be retransmitted to the neighbour (`ospfNbrLsRetransQLen`).

### Routing - VRRP Routers (5 minutes)

Each VRRP virtual router's first-hop redundancy state, administrative state, priority and preemption, with its master and primary addresses. Rows are identified by the interface's ifIndex and the virtual router ID.

From `VRRP-MIB::vrrpOperTable`.

- `State`: VRRP state (1 initialize, 2 backup, 3 master) (`vrrpOperState`).
- `AdminState`: Administrative state (1 up, 2 down) (`vrrpOperAdminState`).
- `Priority`: Priority for the master election; 255 means this router owns the address (`vrrpOperPriority`).
- `Preempt`: Whether a higher-priority backup preempts a lower-priority master (TruthValue: 1 true, 2 false) (`vrrpOperPreemptMode`).
- `MasterIp`: Address of the current master (`vrrpOperMasterIpAddr`).
- `PrimaryIp`: This router's address used as the source of its advertisements (`vrrpOperPrimaryIpAddr`).

### Routing - VRRPv3 Routers (5 minutes)

Each VRRP version 3 virtual router's first-hop redundancy state, priority, preemption and accept mode, master and primary addresses, advertisement interval and uptime, for IPv4 and IPv6. Rows are identified by the interface's ifIndex, the virtual router ID and the address type; Routing - VRRP Routers covers VRRP version 2.

From `VRRPV3-MIB::vrrpv3OperationsTable`.

- `State`: Virtual router state (1 initialize, 2 backup, 3 master) (`vrrpv3OperationsStatus`).
- `Priority`: Priority for the master election; 255 means this router owns the address (`vrrpv3OperationsPriority`).
- `Preempt`: Whether a higher-priority virtual router preempts a lower-priority master (TruthValue: 1 true, 2 false) (`vrrpv3OperationsPreemptMode`).
- `AcceptMode`: Whether the master accepts packets addressed to the owner's address (TruthValue: 1 true, 2 false) (`vrrpv3OperationsAcceptMode`).
- `MasterIp`: The master router's real address (InetAddress) (`vrrpv3OperationsMasterIpAddr`).
- `PrimaryIp`: This router's address used as the source of its advertisements (InetAddress) (`vrrpv3OperationsPrimaryIpAddr`).
- `Addresses`: Number of IP addresses associated with the virtual router (`vrrpv3OperationsAddrCount`).
- `AdvertisementSecs`: Seconds between advertisements (`$vrrpv3OperationsAdvInterval / 100.0`).
- `Uptime`: Time since the virtual router left the initialize state, hundredths of a second (TimeTicks, saved raw) (`vrrpv3OperationsUpTime`).

### Routing - Route Table (5 minutes)

Routes in the routing table, from the current and the deprecated IPv4-only count, and valid routes discarded.

From `IP-FORWARD-MIB` scalars.

- `Routes`: Routes in the routing table, IPv4 and IPv6 (`inetCidrRouteNumber`).
- `Ipv4Routes`: IPv4 routes, from the deprecated count older releases answer (`ipCidrRouteNumber`).
- `RouteDiscards`: Valid routes discarded, for example for lack of memory since the previous poll (`delta(inetCidrRouteDiscards)`).
- `RouteDiscardsPerSec`: Valid routes discarded, for example for lack of memory, per second (`rate(inetCidrRouteDiscards)`).

`delta(x)` is the change in counter `x` between the last two polls; `rate(x)` is that change divided by the monitor's polling period in seconds (TES's `$pollingSecs`), a per-second rate.

## Expected variations

A table monitor returns no rows when the device lacks the feature or hardware it covers. An empty table does not necessarily indicate a collection failure: first confirm that the feature is present and configured on the device.

- Assign it by hand to routers and routed switches, beside Network Traffic and Errors and the device's vendor collections.
- Which standard MIBs a device answers, and how fully, is unverified per vendor: a device that does not implement a table returns nothing for its monitor.
- Do not assign it to a device that takes a Cisco collection polling the same MIBs (Cisco IOS Router or Switch), or those objects are polled twice.
- Routing - BGP Peers polls BGP4-MIB, which covers IPv4 peers only. On Junos, the Junos Operational collection's Juniper - BGP Peers polls Juniper's own BGP MIB for the same peers.
- A device without BGP, OSPF or VRRP configured returns nothing for that monitor.

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
