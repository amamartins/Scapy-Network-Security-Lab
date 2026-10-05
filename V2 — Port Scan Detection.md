The second phase of the project expanded the packet sniffer into a basic network security detection tool. 
Instead of simply displaying network traffic, I implemented logic to identify potential port-scanning behavior.

The objective was to detect when a single source was attempting to communicate with an unusually large number of 
unique destination ports within a short period of time.

Scapy's sniff() function allows a callback function to process packets as they are captured, which provided the 
foundation for adding this detection logic.

<img width="596" height="421" alt="scapyv2-2" src="https://github.com/user-attachments/assets/bf36676e-af16-4040-88cf-9c9ad4c17c71" />

## 1. Created the Port Scan Detector

I created a second Python file inside the project:

port_scan_detector.py

Rather than modifying the original packet sniffer, I created a separate version so that V1 remained a simple 
packet-analysis tool while V2 could focus specifically on detection logic.

## 2. Imported the Required Libraries

I imported the Scapy components needed to capture and inspect IP and TCP traffic:

from scapy.all import sniff, IP, TCP

I also imported Python's defaultdict data structure and the time module:

from collections import defaultdict
import time

These additional libraries allowed me to track ports by source and destination and create a time-based detection window.

## 3. Created a Tracking Structure

The core of the detection logic was a dictionary containing a set of destination ports:

connections = defaultdict(set)

The program uses a combination of:

Source IP → Destination IP → Unique Destination Ports

For example, if a host generated traffic toward:

10.0.0.10:22
10.0.0.10:80
10.0.0.10:443
10.0.0.10:8080

the program would associate those ports with that source/destination pair.

I used a set instead of a list because a set automatically eliminates duplicate values.

This is important because repeatedly connecting to the same port should not count as multiple unique ports.

## 4. Defined the Detection Threshold

I established two parameters:

PORT_THRESHOLD = 10
TIME_WINDOW = 10

The detection logic therefore looks for:

10 or more unique destination ports within a 10-second window.

This creates a basic behavioral threshold rather than alerting on every individual connection.

The goal was to distinguish normal individual connections from activity that could resemble network service scanning.


## 5. Added a Time-Based Detection Window

I used Python's time module to determine how long the current observation window had been running:

elapsed = time.time() - start_time

Once the elapsed time reached the configured 10-second window, the program evaluated the tracked connections.

This prevents the detector from simply counting ports indefinitely.

For example:

10 ports over several hours -- Probably not interesting 
10 ports within 10 seconds -- Potential scanning behavior

The time component therefore adds behavioral context to the detection.

## 6. Evaluated the Number of Unique Ports

The detector checks how many unique destination ports were observed:

if len(ports) >= PORT_THRESHOLD:

If the number of unique ports reaches the threshold of 10, the program generates an alert.

The alert displays:

print(
    f"\n🚨 POSSIBLE PORT SCAN DETECTED"
    f"\nSource: {source}"
    f"\nTarget: {destination}"
    f"\nUnique Ports: {len(ports)}"
    f"\nPorts: {sorted(ports)}\n"
)

* I tested 10, then 2, then back to 10

## 7. Reset the Detection Window

After evaluating the traffic, I cleared the tracking dictionary:

connections.clear()

I then reset the timer:

start_time = time.time()

This begins a new observation window.

The detector therefore continuously evaluates traffic 
in separate 10-second windows rather than allowing previous activity to affect future detections.

## 8. Tested the Detection Logic

<img width="329" height="152" alt="scapyv2-5" src="https://github.com/user-attachments/assets/986bc00f-0cd2-42da-862b-1bcf8d10611e" />
<img width="456" height="471" alt="scapyv2-6-p2" src="https://github.com/user-attachments/assets/58d8fa25-3691-4e4e-b3c6-28f6cb38fa22" />

To verify that the detection logic was working, I temporarily changed the threshold from:

PORT_THRESHOLD = 10

to:

PORT_THRESHOLD = 2

This was done strictly for testing purposes so that I could generate enough controlled traffic to trigger the detector without having to perform a large scan.

I then generated connection attempts against multiple ports on the local system.

After the detector successfully produced an alert, I restored the configuration to:

PORT_THRESHOLD = 10

The final version therefore uses the higher threshold, while the lower threshold was only used to validate that the detection
logic functioned correctly.

## 9. Security Analysis

Port scanning is commonly associated with network service discovery, where an actor attempts to determine which services are accessible on a target system.

The detector therefore provides a basic example of behavioral network detection.

However, an alert does not automatically mean that malicious activity has occurred.

Legitimate applications, vulnerability scanners, administrators, and security tools can also generate connections to multiple ports.

For that reason, the alert is intentionally labeled:

POSSIBLE PORT SCAN DETECTED

rather than:

PORT SCAN CONFIRMED

Additional context would be required before treating the activity as malicious.

