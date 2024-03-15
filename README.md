# Advanced DNS Analysis

## Overview
Advanced DNS Analysis is a comprehensive forensic tool designed for the in-depth analysis of DNS traffic to identify suspicious activities and uncover covert communication through DNS tunneling, specifically focusing on the use of DNS NULL records for smuggling IP packets.

## Key Features
- Detailed DNS traffic analysis to spot unusual activities.
- Identification and investigation of DNS tunneling instances.
- Capability to analyze packet capture (pcap) files for signs of data exfiltration.

## Getting Started

### Prerequisites
- Wireshark for packet capture analysis.
- tcpdump for network traffic filtering and analysis.
- ngrep for pattern searching within pcap files.

## Usage
Follow these steps to analyze a pcap file for suspicious DNS activity:

1. **Open the pcap file in Wireshark:**
```
wireshark evidence-network-tunneling.pcap
```
2. **Analyze the traffic using tcpdump:**
```
tcpdump -tnn -r evidence-network-tunneling.pcap | grep -v NULL
```
3. **Search for specific patterns with ngrep:**
```
ngrep -I evidence-network-tunneling.pcap -X "4500" -t -x 'udp'
```



