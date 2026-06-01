Phase 1: Disk Preparation (fdisk)
Initialize disk: fdisk /dev/nvme0n1

Create GPT table: g

Create Partitions (n):

Partition 1 (EFI): +1G

Partition 2 (Boot): +1G

Partition 3 (Root/LVM): Default (the rest of the space)

Verify: p (Check for overlaps/errors)

Write changes: w (The point of no return)

Phase 2: Filesystem & Encryption
Format EFI: mkfs.fat -F 32 /dev/nvme0n1p1

Format Boot: mkfs.ext4 /dev/nvme0n1p2

Encrypt Root: cryptsetup luksFormat /dev/nvme0n1p3

Open Encryption: cryptsetup open --type luks /dev/nvme0n1p3 lvm

Initialize LVM:

pvcreate /dev/mapper/lvm

vgcreate volgroup0 /dev/mapper/lvm

lvcreate -L 30GB volgroup0 -n lv_root

lvcreate -L 800GB volgroup0 -n lv_home

Activate LVM:

modprobe dm_mod

vgscan

vgchange -ay

Format Logical Volumes:

mkfs.ext4 /dev/volgroup0/lv_root

mkfs.ext4 /dev/volgroup0/lv_home

Phase 3: Mounting & Installation
Mount Root: mount /dev/volgroup0/lv_root /mnt

Mount Boot:

mkdir /mnt/boot

mount /dev/nvme0n1p2 /mnt/boot

Mount Home:

mkdir /mnt/home

mount /dev/volgroup0/lv_home /mnt/home

Mount EFI:

mkdir /mnt/boot/EFI

mount /dev/nvme0n1p1 /mnt/boot/EFI

Install Base System: pacstrap -i /mnt base linux linux-firmware

Generate fstab: genfstab -U -p /mnt >> /mnt/etc/fstab

Phase 4: System Configuration (Chroot)
Enter System: arch-chroot /mnt

User/Pass:

passwd

useradd -m -g users -G wheel [username]

passwd [username]

Install Packages:
pacman -S base-devel dosfstools grub efibootmgr gnome gnome-tweaks lvm2 mtools nano networkmanager os-prober sudo linux-headers linux-lts linux-lts-headers

Configure Hooks:

Edit /etc/mkinitcpio.conf and update HOOKS line to include: ... block sd-encrypt lvm2 filesystems ...

Generate images: mkinitcpio -P

Phase 5: Bootloader & Finalization
Kernel Parameters:

Generate UUID: blkid -s UUID -o value /dev/nvme0n1p3

Edit /etc/default/grub: Add cryptdevice=UUID=[your-uuid]:lvm root=/dev/volgroup0/lv_root to GRUB_CMDLINE_LINUX.

Install GRUB:

grub-install --target=x86_64-efi --bootloader-id=grub_uefi --recheck

cp /usr/share/locale/en\@quot/LC_MESSAGES/grub.mo /boot/grub/locale/en.mo

grub-mkconfig -o /boot/grub.cfg

Enable Services:

systemctl enable gdm

systemctl enable NetworkManager

Exit & Reboot:

exit

umount -a

reboot