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
    
    - Set Hostname: `echo "arch" > /etc/hostname`
        
    - Set Password: `passwd`
        
    - Create User: `useradd -m <username>` && `passwd <username>`

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
        
        ```
        su - to
        mkdir -p ~/build cd ~/build
        git clone https://aur.archlinux.org/yay-bin.git cd yay-yay makepkg -s
        ```
        

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









4. cfdisk /dev/sda
5. gpt
6. New create 500Mb for sda1
7. New create the rest for sda2
8. formatting
	1. mkfs.fat -F 32 /dev/sda1
	2. mkfs.btrfs -f /dev/sda2
9. mounting
	1. mount /dev/sda2 /mnt
	2. btrfs subvolume create /mnt/@home
	3. btrfs subvolume create /mnt/@snapshots
	4. btrfs subvolume create /mnt/@var_log
10. umount /mnt
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
23. vim /etc/systemd/zram-generator.conf
[zram0]
zram-size = ram / 2
compression-algorithm = zstd
swap-priority = 100
mount-point = /dev/zram0

24. pacman -S grub efibootmgr
25. mkdir -p /boot/efi
26. mount /dev/sda1 /boot/efi
27. grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=GRUB (if installs with no error run the following command)
28. grub-mkconfig -o /boot/grub/grub.cfg
29. pacman -S networkmanager sddm
30. systemctl enable NetworkManager
31. systemctl enable sddm
32. pacman -S gnome
33. pacman -S base-devel git
34. su - <your_username>
35. git clone https://aur.archlinux.org/yay.git
36. cd yay
	makepkg -si
37. cd ..
	rm -rf yay
	37. To install a package: yay -S <package_name>
	38. To update everything (official + AUR): yay
	39. To search for a package: yay <search_term>
	40. sudo pacman -S alacritty (inside yay)
**Exit the `chroot`:**
umount -R /mnt
reboot