# Advanced DNS Analysis for Covert Tunnel Detection

## Case Summary

Ann Dercover has located a text file containing super-secret missile launch codes within the Secret Underground Nuclear Missile Facility. With the network being closely monitored and without any physical means to exfiltrate the data, Ann aims to export the missile codes over the network clandestinely.

Security analysts at the facility detect unusual DNS traffic from the internal IP address 192.168.1.30. The forensic investigator's task is to analyze this DNS traffic, determine its purpose, recover information about the systems involved, and assess the risk of data exfiltration.

## Table of Contents
1. [Introduction](#introduction)
2. [Tools and Libraries Used](#tools-and-libraries-used)
3. [Case Summary](#case-summary)
4. [Network Layout](#network-layout)
5. [Evidence Provided](#evidence-provided)
6. [Analysis](#analysis)
7. [Conclusion](#conclusion-)

## Network Layout

- **Internal Network**: `192.168.1.0/24`
- **DMZ**: `10.1.1.0/24`
- **"Internet"**: `172.16.0.0/12`

The system `10.1.1.20` is identified as the internal DNS server.

Tools and Libraries Used
- **`Wireshark`**: Used for capturing and analyzing the network packets.
- **`tcpdump`**: Command-line utility for network monitoring and data acquisition.
- **`tshark`**: Command-line version of Wireshark for automated, scriptable tasks.
- **`ngrep`**: A network packet analyzer that uses regular expressions to search for and match patterns in network traffic.
- **`Bless Hex Editor`**: A high quality, full-featured hex editor used to dissect and carve binary data from the packet capture.
- **`Capinfos`**: A program that reads a saved capture file and returns any or all of several statistics about that file
- **`Iodine`**: A tool for tunneling IPv4 data through a DNS server, suspected to be used in this case.


## Evidence Provided

- `evidence-network-tunneling.pcap`: Packet capture file containing all traffic related to `192.168.1.30`.

## Analysis

### Protocol Statistics

Initial examination using Wireshark's "Protocol Hierarchy Statistics" reveals a high volume of DNS traffic over UDP, exclusively from IP `192.168.1.30`.

![image](https://github.com/anvithalolla/Advanced-DNS-Analysis/assets/55392153/2d94518f-9519-48c7-abae-3d0f01bb4c5e)

### DNS Analysis

DNS traffic was further scrutinized, uncovering a series of DNS NULL queries to a suspicious domain, suggesting the use of a DNS tunneling method to potentially smuggle data.
![image](https://github.com/anvithalolla/Advanced-DNS-Analysis/assets/55392153/8577fae4-9611-4d7f-a643-2d6c3e5c65d0)

![image](https://github.com/anvithalolla/Advanced-DNS-Analysis/assets/55392153/fb4cff87-da86-4f5c-a131-448b06677c3e)

### Quest for Tunneled IP Packets

Investigation hints at the possibility of IP packets being tunneled through DNS NULL records. The `ngrep` tool was utilized to identify payloads containing the IPv4 signature "0x4500".
![image](https://github.com/anvithalolla/Advanced-DNS-Analysis/assets/55392153/3adce9ba-6f4b-4588-b28c-4a03eae675a9)
![image](https://github.com/anvithalolla/Advanced-DNS-Analysis/assets/55392153/78857416-f086-440b-b357-25952bcd60e9)

### Tunneled IP Packet Analysis

Upon isolating the DNS NULL record responses, we discovered encapsulated IP packets, further dissected to reveal source and destination IP addresses within a nonroutable range, commonly used for DNS tunneling.
-	Source and Destination IPv4 Addresses
Analysis determined the source IP address as `10.4.4.2` and the destination as `10.4.4.1`, both nonroutable and indicative of a localized tunneling setup.
![image](https://github.com/anvithalolla/Advanced-DNS-Analysis/assets/55392153/47f8fbb9-c1a8-4450-b2d4-e5e2fe848b99)
![image](https://github.com/anvithalolla/Advanced-DNS-Analysis/assets/55392153/c455a466-643b-4831-af4c-7324170da932)

### Tunneled TCP Segment Analysis
Further dissection of the TCP segment indicated an SSH session in progress, based on port usage and TCP flag settings.

![image](https://github.com/anvithalolla/Advanced-DNS-Analysis/assets/55392153/4732e8a2-7934-468d-b7fb-73d2e26869d9)

## Theory of the Case

- Ann likely established a DNS tunnel using tools like iodine to encapsulate and securely transmit the launch codes over DNS queries.
- The DNS server `10.1.1.20` handled these requests, forwarding them to an external authoritative server at `172.16.16.220`.
- Encapsulated traffic suggests SSH protocol usage, commonly associated with secure, encrypted data transfer methods.

---

This analysis provides a comprehensive understanding of the DNS tunneling technique used in this case, offering insights into both the method and the potential content of the tunneled data.
