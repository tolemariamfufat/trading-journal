## The "Short Way" Forward (For Future Setups)

If you ever have to do this again on a new Kinoite machine, here is the 2-minute version:

1. **Install the packages:** `rpm-ostree install ibus-m17n m17n-db-amharic` (then reboot).
    
2. **Activate the Bridge:** Go to **System Settings > Keyboard > Virtual Keyboard** and select **IBus Wayland**.
    
3. **Add the Language:** Run `ibus-setup`, go to **Input Method**, and add **Amharic - Sera**.
    
4. **If it doesn't show up in the list:** Run this "wake up" command: `printf '#!/bin/sh\n/usr/libexec/ibus-engine-m17n --ibus &\n' > ~/.config/plasma-workspace/env/ibus-m17n.sh && chmod +x ~/.config/plasma-workspace/env/ibus-m17n.sh`
    
5. **Log out and Log back in.**