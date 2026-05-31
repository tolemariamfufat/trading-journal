1. cfdisk /dev/sda
2. gpt
3. New create 500Mb for sda1
4. New create the rest for sda2
5. formatting
	1. mkfs.fat -F 32 /dev/sda1
	2. mkfs.btrfs -f /dev/sda2
6. mounting
	1. mount /dev/sda2 /mnt
	2. btrfs subvolume create /mnt/@home
	3. btrfs subvolume create /mnt/@snapshots
	4. btrfs subvolume create /mnt/@var_log
7. umount /mnt
8. mount -o subvol=@,noatime,compress=zstd /dev/sda2 /mnt
9. mkdir -p /mnt/{boot,home,.snapshots,var/log} (or mkdir -p /mnt/boot /mnt/home /mnt/ .snapshots /mnt/var/log)
10. mount -o subvol=@home,noatime,compress=zstd /dev/sda2 /mnt/home
11. mount -o subvol=@snapshots,noatime,compress=zstd /dev/sda2 /mnt/.snapshots
12. mount -o subvol=@var/log,noatime,compress=zstd /dev/sda2 /mnt/var/log
13. mount /dev/sda1 /mnt/boot