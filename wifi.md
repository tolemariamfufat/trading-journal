- ## 1. Lock your Connection to this Hotspot

First, let's stop your Fedora system from "looking around" at other networks, which causes a 1–2 second freeze every time it scans.
Run this command:
nmcli connection modify "HUAWEI_E5783_CB42" 802-11-wireless.bssid "A2:D6:EA:42:CB:8D"
**Run this command:**
- o undo the change and allow the connection to find the hotspot (or any router with that name) automatically again, run:
Bash

nmcli connection modify "HUAWEI_E5783_CB42" 802-11-wireless.bssid ""