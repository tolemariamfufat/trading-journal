How can check system update like this: How to check the logs later

  

If you ever want to verify that the updates actually happened while you were sleeping or working, you can check the history of that specific service with this command:

Bash
 

journalctl --user -u flatpak-auto-update.service
journalctl -u rpm-ostreed-automatic.service


1) sudo mkdir -p /etc/sddm/themes
2) sudo cp -a /usr/share/sddm/themes/breeze /etc/sddm/themes/custom-breeze
3) verify: ls /etc/sddm/themes/custom-breeze
4) create config: sudo nano /etc/sddm.conf.d/theme.conf
5) toml: 
[Theme]
ThemeDir=/etc/sddm/themes
Current=custom-breeze

6) use it: sudo cp ~/Pictures/my-wallpaper.jpg /etc/sddm/themes/custom-breeze/background.png
7) 