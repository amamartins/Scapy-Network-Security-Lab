The python tool used helped do the following:

Captured live network traffic

Extracted source/destination IPs

Identified TCP, UDP, and ICMP

Extracted source/destination ports

Used Scapy's packet-sniffing functionality

It was quite simple:

<img width="422" height="477" alt="scapy3" src="https://github.com/user-attachments/assets/f7d42c77-d649-452d-9584-eb074d4e130c" />

The first phase of the project focused on building a basic network packet analyzer using Python and Scapy. The goal was to capture live network traffic and extract useful information from each packet, including source and destination IP addresses, protocols, and port numbers.

## 1. Set Up the Python Environment

I created a dedicated project directory for the Scapy networking lab and created a Python file named:

packet_sniffer.py

I then installed the required Python library:

pip install scapy

Because I was running the project on Windows, I also installed Npcap, which allows applications such as Scapy and Wireshark to capture network traffic.

## 2. Imported Scapy Networking Components

Inside packet_sniffer.py, I imported the Scapy functions and protocol layers needed for the project:

from scapy.all import sniff,
 IP, TCP, UDP, ICMP

Each component served a specific purpose:

sniff — captures packets from the network interface
IP — allows the program to access IP-layer information
TCP — identifies and analyzes TCP packets
UDP — identifies and analyzes UDP packets
ICMP — identifies ICMP traffic such as ping requests and replies

## 3. Created the Packet Analysis Function

I created a function called analyze_packet() that processes each packet captured by Scapy.

The first check determines whether the packet contains an IP layer:

if IP not in packet:
    return

This prevents the program from attempting to analyze packets that do not contain the information needed for this project.

## 4. Extracted Source and Destination IP Addresses

For packets containing an IP layer, I extracted the source and destination addresses:

source = packet[IP].src
destination = packet[IP].dst

This allowed the program to determine:

Who sent the packet → Who received the packet

For example:

192.168.1.25 → 142.250.72.14

This provides the basic context needed to understand network communications.

## 5. Identified Network Protocols

I then checked which transport or network protocol was present.

For TCP:

if TCP in packet:
    protocol = "TCP"

For UDP:

elif UDP in packet:
    protocol = "UDP"

And for ICMP:

elif ICMP in packet:
  protocol = "ICMP"

## 6. Result

The final program successfully captured live network traffic and displayed:

Source IP addresses
Destination IP addresses
Network protocols
Source ports
Destination ports

This established the foundation for the second phase of the project.

<img width="548" height="451" alt="scapy2" src="https://github.com/user-attachments/assets/0f5a2108-202c-436e-ba7b-a702756d231e" />
