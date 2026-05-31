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
	(regular: mount -o subvol=@ /dev/sda2 /mnt
mkdir -p /mnt/home /mnt/var /mnt/.snapshots
mount -o subvol=@home /dev/sda2 /mnt/home
mount -o subvol=@var /dev/sda2 /mnt/var
mount -o subvol=@snapshots /dev/sda2 /mnt/.snapshots)

(the following are advnced ways )
1. mount -o subvol=@,noatime,compress=zstd /dev/sda2 /mnt
2. mkdir -p /mnt/{boot,home,.snapshots,var/log} (or mkdir -p /mnt/boot /mnt/home /mnt/ .snapshots /mnt/var/log)
3. mount -o subvol=@home,noatime,compress=zstd /dev/sda2 /mnt/home
4. mount -o subvol=@snapshots,noatime,compress=zstd /dev/sda2 /mnt/.snapshots
5. mount -o subvol=@var/log,noatime,compress=zstd /dev/sda2 /mnt/var/log
6. mount /dev/sda1 /mnt/boot
7. lsblk
8. genfstab -U /mnt >> /mnt/etc/fstab
9. cat /mnt/etc/fstab
10. pacstrap -K /mnt base linux linux-firmware sudo vim
11. arch-chroot /mnt
12. timedatectl list-timezones 
13. timedatectl set-timezone [PLACE]
14. hwclock --systohc
15. vim /etc/locale.gen (uncomment the lince #en_us.UTF-8 UTF-8)
16. vim /etc/locale.conf (write this line in it: LANG=en_US.UTF-8)
17. vim /etc/hostname (write arch)
18. vim /etc/hosts (check if it is correct)
19. passwd
20. useradd -m to
21. passwd to
22. pacman -S zram-generator
23. 