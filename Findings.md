## Limitations and Future Improvements

The current detector is intentionally simple and was designed as a learning project.

Potential improvements include:

Tracking TCP SYN packets specifically
Monitoring multiple source/destination pairs independently
Using a sliding time window instead of fixed 10-second windows
Adding configurable thresholds
Detecting horizontal scanning across multiple hosts
Adding UDP scanning detection
Writing alerts to a log file
Integrating the detection with a SIEM such as Wazuh
Adding automated response capabilities
Reducing false positives through baseline network behavior

These improvements would move the project from a basic Python proof-of-concept toward a more complete network detection system.

## Result

V2 extended the original packet sniffer into a basic network security detection tool.

The project progressed from:

V1: What traffic is occurring?

to:

V2: Does this traffic exhibit behavior that could indicate reconnaissance?

This demonstrated the use of Python and Scapy for packet capture, network analysis,
behavioral detection, and basic security alerting.
Result

V2 extended the original packet sniffer into a basic network security detection tool.
