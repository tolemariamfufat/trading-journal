=### Phase 1: Disk Partitioning and Formatting
iwctl
device list
station wlan0 scan
station wlan0 get-networks
station wlan0 connect [Your Network]
exit
png -c 3 archlinux.org

setfont ter-124b
timedatectl set-ntp true
cfdisk 

1. **Partitioning:** `cfdisk (Select **GPT** label).
    
    - `sda1`: 512Mb (EFI System)
        
    - `sda2`: Remainder (Linux Filesystem)
   lsblk
1. **Formatting:**
    
    - `mkfs.fat -F 32 /dev/sda1`
        
    - `mkfs.btrfs -f /dev/sda2`

### Phase 2: BTRFS Subvolume Setup

1. **Initial Mount:** `mount /dev/sda2 /mnt`
    
2. **Create Subvolumes:**
    
    Bash
    
    ```
    btrfs subvolume create /mnt/@
    btrfs subvolume create /mnt/@home
    btrfs subvolume create /mnt/@snapshots
    btrfs subvolume create /mnt/@var_log
    ```
    
3. **Unmount and Remount (with Advanced Options):**
    
    Bash
    
    ```
    umount /mnt
    mount -o subvol=@,noatime,compress=zstd /dev/sda2 /mnt
    mkdir -p /mnt/{boot,home,.snapshots,var/log}
    mount -o subvol=@home,noatime,compress=zstd /dev/sda2 /mnt/home
    mount -o subvol=@snapshots,noatime,compress=zstd /dev/sda2 /mnt/.snapshots
    mount -o subvol=@var_log,noatime,compress=zstd /dev/sda2 /mnt/var/log
    mount /dev/sda1 /mnt/boot
    ```
### Phase 3: System Installation

1. **Generate FSTAB:** `genfstab -U /mnt >> /mnt/etc/fstab`
    
2. **Base Install:** `pacstrap -K /mnt base linux linux-firmware sudo vim`
    
3. **Enter Chroot:** `arch-chroot /mnt`
### Phase 4: System Configuration

1. **Time & Locale:**
    
    - `timedatectl set-timezone <Region/City>`
        
    - `hwclock --systohc`
        
    - `vim /etc/locale.gen` (uncomment `en_US.UTF-8 UTF-8`)
        
    - `echo "LANG=en_US.UTF-8" > /etc/locale.conf`
        
2. **Network & Security:**
    
    - Set Hostname: vim /etc/hostname (`echo "arch" > /etc/hostname`)
        set host: vim /etc/host (no need to edit it should be there)
    - Set Password: `passwd`
        
    - Create User: `useradd -m <username>` && `passwd <username>`
		usermod -aG wheel,audio,video,optical,storage,input to
		visudo (to edit the file)
		uncomment to allow members of group wheel to execute any command
		%wheel ALL (ALL:ALL) ALL
### Phase 5: Optimization & Bootloader

1. **ZRAM Setup:**
    
    - `pacman -S zram-generator`
        
    - Edit `/etc/systemd/zram-generator.conf`:
        
        Ini, TOML
        
        ```
        [zram0]
        zram-size = ram / 2
        compression-algorithm = zstd
        swap-priority = 100
        ```
        
2. **GRUB Installation:**
    
    - `pacman -S grub efibootmgr`
        
    - `mkdir -p /boot/efi && mount /dev/sda1 /boot/efi`
        
    - `grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=GRUB`
        
    - `grub-mkconfig -o /boot/grub/grub.cfg`


### Phase 6: Finalizing Desktop & AUR

1. **Networking & DM:** `pacman -S networkmanager sddm gnome`
    
2. **Enable Services:**
    
    - `systemctl enable NetworkManager`
        
    - `systemctl enable sddm`
        
3. **AUR Helper (Yay):**
    
    - As normal user:
        
        Bash
        pacman -S --needed base-devel git
        ```
        su - to
        mkdir -p ~/build cd ~/build
        git clone https://aur.archlinux.org/yay-bin.git cd yay-yay makepkg -s
        ```
        exit
        cd /home/to/build/yay-bin
        pacman -U yay-bin-*.pkg.tar.zst

### Exit & Reboot

Bash

```
exit
umount -R /mnt
reboot
```

### Quick Cheat Sheet for `yay`

- **Install:** `yay -S <package_name>`
    
- **Update All:** `yay`
    
- **Search:** `yay <search_term>`

The fdisk Workflow Decoder
lsblk
fdisk /dev/nvme0n1
The typical workflow after that command:
g (Create GPT partition table): This is the modern standard (GUID Partition Table). It replaces the old, limited MBR style. It is required for modern UEFI booting.

n (New partition): You use this to define each segment. It asks for the partition number, the starting sector, and the size (e.g., +512M for EFI).

p (Print): This is your most important safety tool. You use it before you write anything to check your work, and after you create each partition to see the resulting layout (/dev/nvme0n1p1, p2, p3, etc.).

t (Type): This tells the system what the partition is for. For the EFI partition, you typically set the type to "EFI System" (Type 1). Without this, the motherboard won't recognize it as a bootable area.

Why this order is perfect:
Safety First: By running p frequently, you ensure you aren't overlapping partitions or making a size error.

Logical Flow: You create the table (g), build your partitions (n), assign their roles (t), and finally make it permanent (w).

No Mistakes: Because w is the very last step, if you get to p and see a mistake, you can simply quit with q and start over without damaging a single byte of data.
w (Write): The "Point of No Return." This commits your changes to the physical disk. Nothing actually happens to your drive until you press w.
Command (m for help): n
Partition number (1-128, default 1): [Enter]
First sector (2048-..., default 2048): [Enter]
Last sector, +/-sectors or +/-size{K,M,G,T,P}: +1G   <-- You type the size

