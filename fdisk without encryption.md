**EDITOR=nano visudo**

Phase 1: Disk Preparation (fdisk)
sudo fdisk -l
sudo fdisk /dev/sda
lsblk
sudo mkfs.fat -F 32 /dev/sda1
sudo mkfs.ext4 /dev/sda2

Initialize disk: fdisk /dev/nvme0n1

Create GPT table: g

Create Partitions (n):

Partition 1 (EFI): +1G

Partition 2 (Boot): +1G

Partition 3 (Root/LVM): Default (the rest of the space)

Verify: p

Write changes: w

Phase 2: Filesystem & LVM Foundation
# Format partitions
mkfs.fat -F 32 /dev/nvme0n1p1
mkfs.ext4 /dev/nvme0n1p2

# Initialize LVM
pvcreate /dev/nvme0n1p3
vgcreate volgroup0 /dev/nvme0n1p3
lvcreate -L 30GB volgroup0 -n lv_root
lvcreate -L 800GB volgroup0 -n lv_home

# Activate LVM
modprobe dm_mod
vgscan
vgchange -ay

# Format Logical Volumes
mkfs.ext4 /dev/volgroup0/lv_root
mkfs.ext4 /dev/volgroup0/lv_home

Phase 3: Mounting & Installation
# Mount Root
mount /dev/volgroup0/lv_root /mnt

# Mount Boot
mkdir /mnt/boot
mount /dev/nvme0n1p2 /mnt/boot

# Mount Home
mkdir /mnt/home
mount /dev/volgroup0/lv_home /mnt/home

# Mount EFI
mkdir /mnt/boot/EFI
mount /dev/nvme0n1p1 /mnt/boot/EFI

# Install Base
pacstrap -i /mnt base linux linux-firmware

# Generate fstab
genfstab -U -p /mnt >> /mnt/etc/fstab

Phase 4: System Configuration (Chroot)
# Enter System
arch-chroot /mnt

# User/Pass setup
passwd
useradd -m -g users -G wheel [username]
passwd [username]
visudo (in the file uncomment %wheel ALL (ALL:ALL) ALL)
# Install essential packages
pacman -S base-devel dosfstools grub efibootmgr gnome gnome-tweaks lvm2 mtools nano networkmanager os-prober sudo linux-headers linux-lts linux-lts-headers

# Configure Hooks (Edit /etc/mkinitcpio.conf)
# Find the HOOKS line and ensure it contains:
# HOOKS=(base udev autodetect modconf kms keyboard keymap consolefont block lvm2 filesystems fsck)
nano /etc/mkinitcpio.conf

# Generate images
mkinitcpio -p linux (linux-lts)

Phase 5: Bootloader & Finalization
# Install GRUB
grub-install --target=x86_64-efi --bootloader-id=grub_uefi --recheck

# Generate Config
grub-mkconfig -o /boot/grub/grub.cfg

# Enable Services
systemctl enable gdm
systemctl enable NetworkManager

# Exit & Reboot
exit
umount -R /mnt
reboot
sudo pacman -S --needed git base-devel
git clone https://aur.archlinux.org/yay-bin.git
cd yay-bin
makepkg -si

Before you run the final genfstab or grub-mkconfig commands, take a moment to look at your work.

Verify your mount points: Run lsblk. This command will display your entire storage tree. Look at your EFI, Boot, and LVM volumes. If they aren't mounted exactly where you intended them to be, fstab will be wrong, and your system won't boot.

Verify your LVM status: Run vgs and lvs. This confirms that your Volume Group is active and your logical volumes are correctly sized.

Verify your internet connectivity: Before you trigger a massive pacstrap or pacman -S install, run ping -c 3 archlinux.org. It is better to know your network is down before you start a 10-minute download than halfway through.