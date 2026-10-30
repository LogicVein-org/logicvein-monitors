# Linux Host Health and Performance Collection

Last updated: 2026-10-30

Tested against: Not yet validated against a device.

This monitor set is for: Hosts running the Net-SNMP agent.

The collection is a starting point. It defines no alert policies or thresholds.

Interface traffic and errors are not included: assign **Standard Network Interfaces - Health and Performance Collection** to the same devices for those.

## Requirements

Before importing the monitor set, confirm that:

- The device has been added to the ThirdEye inventory.
- ThirdEye can reach the device's SNMP agent, normally over UDP port 161.
- A working SNMP credential has been assigned to the device in ThirdEye. SNMPv3 with authentication and privacy is recommended.
- The MIB modules listed below are loaded in ThirdEye.

### MIB modules

The collection uses these modules:

- `UCD-DISKIO-MIB`
- `UCD-SNMP-MIB`

ThirdEye's built-in MIB store already includes all of these modules, so no MIB import is needed. The MIB files are not included with this collection; to install or update a module, obtain the files as described below and import them with their dependencies.

### Obtaining the Net-SNMP MIBs

`UCD-SNMP-MIB` and `UCD-DISKIO-MIB` describe the Net-SNMP agent and are distributed with Net-SNMP's own MIB files.

## Installation

1. In **Inventory**, use **Tools > SNMP System Info** to verify that ThirdEye can query each target device.
2. Go to **Monitors > Sets** and import `Linux Host - Health and Performance Collection.3em`.
3. Assign **Linux Host - Health and Performance Collection** to the appropriate devices.
4. Wait at least 5 minutes so every monitor has had a collection opportunity, then confirm that current data is present for the applicable monitors.

Automatic assignment to newly discovered devices is disabled in the set. Review the set before enabling automatic assignment in your environment.

These host queries match the devices the set is meant for: `sysObjectID=1.3.6.1.4.1.8072.*`.

## Included monitors

The set contains 6 SNMP monitors. Metrics carry friendly names; each lists the MIB object it reads or the formula it computes.

### Linux - CPU (5 minutes)

CPU time split by state over the last poll, from the raw tick counters (cumulative over all CPUs).

From `UCD-SNMP-MIB` scalars.

- `BusyPct`: User, nice and system time as a percentage of all CPU time since the previous poll (`((delta(ssCpuRawUser) + delta(ssCpuRawNice) + delta(ssCpuRawSystem)) / (delta(ssCpuRawUser) + delta(ssCpuRawNice) + delta(ssCpuRawSystem) + delta(ssCpuRawIdle) + delta(ssCpuRawWait))) * 100.0`).
- `UserPct`: User time as a percentage of all CPU time since the previous poll (`(delta(ssCpuRawUser) / (delta(ssCpuRawUser) + delta(ssCpuRawNice) + delta(ssCpuRawSystem) + delta(ssCpuRawIdle) + delta(ssCpuRawWait))) * 100.0`).
- `SystemPct`: System time as a percentage of all CPU time since the previous poll (`(delta(ssCpuRawSystem) / (delta(ssCpuRawUser) + delta(ssCpuRawNice) + delta(ssCpuRawSystem) + delta(ssCpuRawIdle) + delta(ssCpuRawWait))) * 100.0`).
- `IoWaitPct`: Time waiting for I/O as a percentage of all CPU time since the previous poll (`(delta(ssCpuRawWait) / (delta(ssCpuRawUser) + delta(ssCpuRawNice) + delta(ssCpuRawSystem) + delta(ssCpuRawIdle) + delta(ssCpuRawWait))) * 100.0`).

### Linux - Memory (5 minutes)

Physical memory and swap, with buffers and cache.

From `UCD-SNMP-MIB` scalars.
Covers the TES built-in template *Net-SNMP - MemoryInformation*: it polls every object that template polls, under its own names.

- `TotalKB`: Physical memory installed, kB (`memTotalReal`).
- `AvailableKB`: Physical memory unused or available, kB (`memAvailReal`).
- `BuffersKB`: Memory allocated as buffers, kB (`memBuffer`).
- `CachedKB`: Memory allocated as cache, kB (`memCached`).
- `UsedPct`: Memory in use, not counting buffers and cache, as a percentage of the total (`(($TotalKB - $AvailableKB - $BuffersKB - $CachedKB) / $TotalKB) * 100.0`).
- `SwapTotalKB`: Swap space configured, kB (`memTotalSwap`).
- `SwapAvailableKB`: Swap space unused, kB (`memAvailSwap`).
- `SwapUsedPct`: Swap in use as a percentage of the swap configured (`(($SwapTotalKB - $SwapAvailableKB) / $SwapTotalKB) * 100.0`).
- `TotalFreeKB`: Memory free or available, real and swap together, kB (`memTotalFree`).
- `SharedKB`: Memory allocated as shared memory, kB (not implemented on every operating system) (`memShared`).
- `SwapError`: Available swap below the configured minimum (0 no error, 1 error) (`memSwapError`).

### Linux - System Activity (5 minutes)

Swapping, block I/O, interrupts and context switches, as rates and as counts since the previous poll, from the raw counters.

From `UCD-SNMP-MIB` scalars.

- `SwapInBlocksPerSec`: Blocks swapped in, per second (`rate(ssRawSwapIn)`).
- `SwapInBlocks`: Blocks swapped in since the previous poll (`delta(ssRawSwapIn)`).
- `SwapOutBlocksPerSec`: Blocks swapped out, per second (`rate(ssRawSwapOut)`).
- `SwapOutBlocks`: Blocks swapped out since the previous poll (`delta(ssRawSwapOut)`).
- `BlocksSentPerSec`: Blocks sent to block devices, per second (`rate(ssIORawSent)`).
- `BlocksSent`: Blocks sent to block devices since the previous poll (`delta(ssIORawSent)`).
- `BlocksReceivedPerSec`: Blocks received from block devices, per second (`rate(ssIORawReceived)`).
- `BlocksReceived`: Blocks received from block devices since the previous poll (`delta(ssIORawReceived)`).
- `InterruptsPerSec`: Interrupts processed, per second (`rate(ssRawInterrupts)`).
- `Interrupts`: Interrupts processed since the previous poll (`delta(ssRawInterrupts)`).
- `ContextSwitchesPerSec`: Context switches, per second (`rate(ssRawContexts)`).
- `ContextSwitches`: Context switches since the previous poll (`delta(ssRawContexts)`).

### Linux - Load Average (5 minutes)

The 1, 5 and 15 minute load averages, one row each.

From `UCD-SNMP-MIB::laTable`, one row per `laNames`.

- `LoadX100`: Load average multiplied by 100 (`laLoadInt`).

### Linux - Disk Space (5 minutes)

Space on each disk the agent is configured to watch (the snmpd.conf disk directive).

From `UCD-SNMP-MIB::dskTable`, one row per `dskPath`.
Covers the TES built-in template *Net-SNMP - DiskInformation*: it polls every object that template polls, under its own names.

- `UsedPct`: Space used, percent (`dskPercent`).
- `TotalKB`: Total size, kB (latches at 2147483647 above 2 TB) (`dskTotal`).
- `UsedKB`: Space used, kB (latches at 2147483647 above 2 TB) (`dskUsed`).
- `AvailableKB`: Space available, kB (latches at 2147483647 above 2 TB) (`dskAvail`).
- `InodesUsedPct`: Inodes used, percent (`dskPercentNode`).
- `Error`: Below the minimum space configured for it (0 no error, 1 error) (`dskErrorFlag`).

### Linux - Disk I/O (5 minutes)

Bytes and operations per block device, as rates and as volumes since the previous poll.

From `UCD-DISKIO-MIB::diskIOTable`, one row per `diskIODevice`.
Covers the TES built-in template *Disk I/O Stats*: it polls every object that template polls, under its own names.

- `BytesReadPerSec`: Bytes read, per second (`rate(diskIONReadX)`).
- `BytesWrittenPerSec`: Bytes written, per second (`rate(diskIONWrittenX)`).
- `BytesRead`: Bytes read since the previous poll (`delta(diskIONReadX)`).
- `BytesWritten`: Bytes written since the previous poll (`delta(diskIONWrittenX)`).
- `ReadsPerSec`: Read accesses, per second (`rate(diskIOReads)`).
- `Reads`: Read accesses since the previous poll (`delta(diskIOReads)`).
- `WritesPerSec`: Write accesses, per second (`rate(diskIOWrites)`).
- `Writes`: Write accesses since the previous poll (`delta(diskIOWrites)`).

`delta(x)` is the change in counter `x` between the last two polls; `rate(x)` is that change divided by the monitor's polling period in seconds (TES's `$pollingSecs`), a per-second rate.

## Expected variations

A table monitor returns no rows when the device lacks the feature or hardware it covers. An empty table does not necessarily indicate a collection failure: first confirm that the feature is present and configured on the device.

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
