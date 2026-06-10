# 1. Mount your top-level Btrfs volume to a temporary spot
sudo mount /dev/sda2 /mnt -o subvolid=5

- **What:** This mounts your main Linux partition (`/dev/sda2`) to the `/mnt` directory, specifically targeting `subvolid=5`.
    
- **Why:** In Btrfs, `subvolid=5` is the absolute top-level root of the entire drive. Normally, when you boot Arch, it hides this top level and only shows you the subvolumes (like your system or your home folder). We need to mount the top level so we can see _all_ your subvolumes and snapshots side-by-side.
# 2. Move your current broken system out of the way
sudo mv /mnt/@ /mnt/@_broken

What: This renames your current, active root system subvolume from @ to @_broken.

Why: Linux cannot have two subvolumes with the same name in the same place. Since we want to put a healthy copy of snapshot 66 into the @ slot, we have to move the bad one out of the way first. Because Linux loads everything into your RAM while running, it's safe to rename this folder while you are still actively logged into it.
# 3. Create a fresh, writable copy of snapshot 66 to act as your new root
sudo btrfs subvolume snapshot /mnt/@snapshots/66/snapshot /mnt/@

What: This creates a new, writable subvolume named @ located at /mnt/@, using your read-only snapshot 66 as the exact blueprint.

Why: Snapper snapshots are usually read-only. You can't just boot directly into them and start using your computer normally because Linux needs to write logs and temporary files. This command takes a perfect copy of how your system looked at snapshot 66 and makes it writable again, placing it right where Arch expects to find your system (@).
# 4. Reboot your machine
sudo reboot
What: Restarts your computer.

Why: Your current session is still technically running off the old code in RAM. Rebooting forces your computer's bootloader (like GRUB or systemd-boot) to look at the drive, find the brand-new @ subvolume we just created, and boot into your restored system.
# What to do after the reboot:
sudo mount /dev/sda2 /mnt -o subvolid=5
sudo btrfs subvolume delete /mnt/@_broken
sudo umount /mnt
