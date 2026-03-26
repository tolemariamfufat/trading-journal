How can check system update like this: How to check the logs later

  

If you ever want to verify that the updates actually happened while you were sleeping or working, you can check the history of that specific service with this command:

Bash

  

journalctl --user -u flatpak-auto-update.service