1. REBOOT -> Select the 2nd option (Rollback) in the GRUB menu.
2. OPEN TERMINAL -> Lock down your safe haven:
   sudo ostree admin pin 2
3. WORK SAFELY -> (Wait for the Universal Blue team to patch the bug).
4. UNPIN LATER -> When the fix is live, restore normal updates:
   sudo ostree admin pin --unpin 2