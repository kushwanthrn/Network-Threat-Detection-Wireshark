# Network Threat Detection
*Reverse Shell & C2 Beaconing — Packet-Level Analysis and Malicious/Benign Traffic Discrimination*

## Overview

This project analyzes two core post-exploitation network techniques — an interactive reverse shell and periodic C2 beaconing — entirely from raw packet capture in Wireshark, without relying on endpoint telemetry or a SIEM. It's a deliberately different analytical skill from [End-to-End SOC Investigation](https://github.com/kushwanthrn/SOC-End-to-End-Investigation) and [SIEM Detection Engineering with Rule Tuning](https://github.com/kushwanthrn/SIEM-Detection-Engineering-Rule-Tuning): reading attacker behavior directly off the wire rather than through correlated logs.

Each technique was captured twice — once as a genuine malicious simulation, once as a carefully designed benign lookalike — to identify the specific, articulable indicators that actually separate malicious traffic from traffic that looks superficially similar. Both true-positive captures were independently corroborated across three sources (script execution log, server-side log, and the packet capture itself), which agreed closely in every case.

**📄 [Full Write-Up (PDF)](./Network-Threat-Detection-Write-Up.pdf)**

## Key Findings

- **Reverse shell:** packet/byte count alone is a weak signal. The reliable discriminator is **conversational structure** — the true positive showed 8 turns of sustained, bidirectional exchange (67 packets, ~6 minutes); the false positive was a single one-way push and immediate close (8 packets, 25 seconds).
- **Beaconing:** periodicity alone is *not* the signal — both captures show a host repeatedly contacting the same server every 30-45 seconds. The reliable discriminator is **interval variance**: the true positive held a near-mechanical 30.13s ± 0.04s rhythm, while the false positive (a legitimate jittered update-checker) varied by up to 15 seconds between checks.
- **A real debugging story:** the reverse-shell true positive initially connected but was silently non-interactive. Systematic elimination — confirming the read loop, confirming newline handling, fixing a missing stream flush, then diagnosing further into the process I/O redirection configuration — resolved it. Documented in full in the write-up.

## Lab Environment

Same isolated lab as Projects 1 and 2: Kali Linux (attacker/capture point) + Windows 10 (traffic generator) on a sealed VirtualBox NAT Network. Wireshark ran on Kali's interface, capturing the full bidirectional conversation for every test since Kali hosts the listener/server side in each scenario.

## Repository Contents

Each folder contains the full evidence set for that test — Wireshark screenshots (numbered to match the write-up's figures) and the actual `.pcapng` capture file:

```
├── Network-Threat-Detection-Write-Up.pdf
├── Reverse-Shell-True-Positive/
├── Reverse-Shell-False-Positive/
├── Beaconing-True-Positive/
└── Beaconing-False-Positive/
```

## Skills Demonstrated

Wireshark packet analysis (filters, Follow TCP Stream, IO Graphs) · Reverse shell / C2 beaconing detection · Malicious vs. benign traffic discrimination · Interval/timing analysis · Multi-source evidence corroboration · Systematic debugging

---

*Part of a SOC analyst portfolio. See also: [End-to-End SOC Investigation](https://github.com/kushwanthrn/SOC-End-to-End-Investigation) · [SIEM Detection Engineering with Rule Tuning](https://github.com/kushwanthrn/SIEM-Detection-Engineering-Rule-Tuning)*
