#paru -Syu && paru -Rns $(paru -Qdtq)
paru -Syu && paru -Qdtq | xargs -r paru -Rns

sudo nano /etc/timeshift-autosnap.conf
sudo nano /usr/share/libalpm/hooks/00-timeshift-autosnap.hook
sudo nano /etc/timeshift-autosnap.conf
sudo nano /etc/kernel/cmdline

WINEFSYNC=1 wine ~/.wine/drive_c/Program\ Files/MetaTrader\ 5/terminal64.exe

sudo nano /etc/pacman.conf

cat /usr/share/libalpm/hooks/00-timeshift-autosnap.hook

sudo rm dev wlo1 set power_save on


steps to restore from broken root on btrfs
1. mount -o subvolid=5 /dev/sda2 /mnt
2. ls /mnt/timeshift-btrfs/snapshots/
3. btrfs subvolume delete /mnt/@
4. btrfs subvolume snapshot /mnt/timeshift-btrfs/snapshots/2026-06-14_07-29-48/@ /mnt/@
5. umount /mnt
6. mount -o subvol=@ /dev/sda2 /mnt
7. mount /dev/sda1 /mnt/boot
8. arch-chroot /mnt
9. bootctl update  (or grub-mkconfig)
10. exit
11. umount -R /mnt
12. reboot

Rollback on the terminal without usb live boot
1. mount -o subvolid=5 /dev/sda2 /mnt
2. ls /mnt/@snapshots/
3. btrfs subvolume delete /mnt/@ (for rollback)
4. btrfs subvolume snapshot /mnt/@snapshots/12/snapshot /mnt/@
5. umount /mnt
6. reboot 
(if you rollback to Jun 15, 2006: sudo mount -a after reboot)

in short: 
mount -o subvolid=5 /dev/sda2 /mnt
btrfs subvolume delete /mnt/@ (for rollback)
btrfs subvolume snapshot /mnt/@snapshots/12/snapshot /mnt/@

commands:
sudo systemctl disable --now timeshift-cron.timer

sudo systemctl disable --now snapper-timeline.timer
sudo systemctl disable --now snapper-cleanup.timer

sudo rm /etc/pacman.d/hooks/50-swn-pre.hook /etc/pacman.d/hooks/50-swn-post.hook

 sudo btrfs subvolume list / 
 sudo snapper list
 sudo snapper delete <id> (for cleanup)
 
 Experience:
 sudo mount -o subvolid=5 /dev/sda2 /mnt
 ls -l /mnt/@snapshots
 sudo mv /mnt/@ /mnt/@_broken (for rollback)
 sudo btrfs subvolume snapshot /mnt/@snapshots/21/snapshot /mnt/
 sudo umount /mnt