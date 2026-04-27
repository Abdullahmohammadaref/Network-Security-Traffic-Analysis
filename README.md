# SMB Attack diagnostics & recovery - A Wireshark Analysis and Firewall
Network-Security-Traffic-Analysis

## Overview
This analysis focuses on examining and diagnosing a sample capture for a network 
with latency issues that are caused by an SMB attack.

## Problem
The analyzed network consists of several clients who all share one master browser.
If one of the clients sends a request to the master browser but doesn't receive a response then it 
starts emergency elections, forcing all the clients to stop their processes, check is master browser
response, elect new master browser if he doesn't respond, resume back to work.

This network have been experiencing latency issues because one client is constantly initiating 
master browser elections even when a there is a master browser, causing all processes to stop and resume repeatedly.
## Analysis

Applied filters and identified master browser (`192.168.123.2`) and infected browser (`192.168.123.1`).

Discovered that infected browser is not receiving requests from UDP port 138 after applying a `ip.dst == 192.168.123.1 && !(ip.src == 192.168.123.2)` filter.

Its is likely that the infected browser is a victim of an SMB attack, that altered firewall settings to block UDP port 138
which is where the master browser sends its response to a requesting browser.

## Solution

1. Manually go to the effected client device firewall setting and apply a **PERMIT** rule that enable messages from UDP port 138
2. Block all external requests/messages for all clients to prevent this SMB attack from occurring again

## Requirements
- Wireshark

## How to follow?
1. Load the sample capture file inside wireshark
2. Apply the following filters: `(ip.src == 192.168.123.1 || ip.src == 192.168.123.2) && (udp.port == 138 || SMB || SMB2) && frame.number <= 56`

## Resources

- Samplecaptures - wireshark wiki (no date). Available at: https://wiki.wireshark.org/samplecaptures
(Accessed: 3 July 2025).
