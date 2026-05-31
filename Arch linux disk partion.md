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
8. 