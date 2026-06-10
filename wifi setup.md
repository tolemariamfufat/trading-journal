## Your Complete WiFi Fix

|Setting|Value|Location|
|---|---|---|
|Driver power save|OFF|`/etc/modprobe.d/iwlwifi.conf`|
|WiFi 6 (802.11ax)|DISABLED|`/etc/modprobe.d/iwlwifi.conf`|
|Power scheme|Performance (1)|`/etc/modprobe.d/iwlwifi.conf`|
|NM power save|Disabled (2)|`/etc/NetworkManager/conf.d/wifi-powersave.conf`|
|Random MAC scan|Disabled|`/etc/NetworkManager/conf.d/wifi-scan.conf`|

## What Each Fix Does

- **Prevents card from sleeping** → No more slowdown from power saving kicks in
    
- **Disables buggy WiFi 6** → Fixes kernel warning that caused instability
    
- **Disables random MAC** → Prevents periodic disconnects during scans
    
- **Performance mode** → Keeps radio at full power constantly
    

Your WiFi should now stay consistently fast without the gradual degradation you were experiencing. If everything works well for a few days, you're set! 🎉

## What to Do If It Ever Returns

After kernel updates, sometimes configs get reset. Just run:

bash

# Quick check if everything is still applied
iwconfig wlo1 | grep "Power"
cat /etc/modprobe.d/iwlwifi.conf