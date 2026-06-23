# Nmap Scan Detection & Evasion Analysis

**Analyst:** Chirayu Mahendra Palandurkar  
**Date:** 18 June 2026  
**Report ID:** SDR-2026-0618-001  
**Tools:** Nmap 7.99 · Wireshark 4.6.6 · Suricata 8.0.5  
**Lab:** VirtualBox — NAT Network (Kali + Debian)  
**PCAP Source:** [Wireshark Sample Captures — NMap-Captures.zip](https://gitlab.com/wireshark/wireshark/-/wikis/SampleCaptures)

---

## Overview

Fingerprinted 5 Nmap scan types at packet level in Wireshark 
and built 3 Suricata detection rules tested against real Nmap 
PCAPs. Documented a detection gap on low-volume ACK scans.

---

## Scan Types Analysed

| Scan | Flag Signature | Wireshark Filter | Detectable |
|------|---------------|-----------------|------------|
| SYN (-sS) | SYN only | `tcp.flags.syn==1 and tcp.flags.ack==0` | ✅ Yes |
| FIN (-sF) | FIN only | `tcp.flags.fin==1 and tcp.flags.syn==0` | ✅ Yes |
| NULL (-sN) | No flags | `tcp.flags==0` | ✅ Yes |
| Xmas (-sX) | FIN+PSH+URG | `tcp.flags.fin==1 and tcp.flags.push==1 and tcp.flags.urg==1` | ✅ Yes |
| Decoy (-sS -D) | Mixed sources | Correlation required | ⚠️ Hard |

---

## Suricata Detection Rules
---

## Detection Results

| PCAP | Packets | Rule Fired | Result |
|------|---------|------------|--------|
| nmap_standard_scan | 2,004 | SID 9000001 | ✅ Detected |
| nmap_OS_scan | 2,056 | SID 9000001 + 9000003 + 9000002 | ✅ Detected |
| nmap_ACK_scan_on_port_80 | 6 | None | ⚠️ Evaded |

---

## Detection Gap

The ACK-only scan targeting a single port (6 packets) 
evaded all threshold-based rules. Low-volume targeted 
scans require **stateful detection** over longer time 
windows — not just packet counting.

This is a documented gap in many production SOC environments.

---

## MITRE ATT&CK Mapping

| Technique ID | Name |
|---|---|
| T1595.001 | Active Scanning: Scanning IP Blocks |
| T1595.002 | Active Scanning: Vulnerability Scanning |
| T1046 | Network Service Discovery |

