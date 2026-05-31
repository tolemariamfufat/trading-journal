### 1. Disk Preparation and Formatting

- **1.1 Partitioning**
    
    - 1.1.1 Run `cfdisk /dev/sda` and select the **GPT** label.
        
    - 1.1.2 Create `sda1` (500MB) for EFI.
        
    - 1.1.3 Create `sda2` (Remainder) for the main system.
        
- **1.2 Formatting**
    
    - 1.2.1 `mkfs.fat -F 32 /dev/sda1`
        
    - 1.2.2 `mkfs.btrfs -f /dev/sda2`
        

### 2. BTRFS Subvolume Configuration

- **2.1 Initial Setup**
    
    - 2.1.1 `mount /dev/sda2 /mnt`
        
- **2.2 Creating Subvolumes**
    
    - 2.2.1 `btrfs subvolume create /mnt/@`
        
    - 2.2.2 `btrfs subvolume create /mnt/@home`
        
    - 2.2.3 `btrfs subvolume create /mnt/@snapshots`
        
    - 2.2.4 `btrfs subvolume create /mnt/@var_log`
        
- **2.3 Mounting with Advanced Options**
    
    - 2.3.1 Unmount the temporary mount: `umount /mnt`
        
    - 2.3.2 Mount root: `mount -o subvol=@,noatime,compress=zstd /dev/sda2 /mnt`
        
    - 2.3.3 Create directories: `mkdir -p /mnt/{boot,home,.snapshots,var/log}`
        
    - 2.3.4 Mount subvolumes:
        
        - `mount -o subvol=@home,noatime,compress=zstd /dev/sda2 /mnt/home`
            
        - `mount -o subvol=@snapshots,noatime,compress=zstd /dev/sda2 /mnt/.snapshots`
            
        - `mount -o subvol=@var_log,noatime,compress=zstd /dev/sda2 /mnt/var/log`
            
    - 2.3.5 Mount boot: `mount /dev/sda1 /mnt/boot`
        

### 3. System Installation

- **3.1 Base Environment**
    
    - 3.1.1 `genfstab -U /mnt >> /mnt/etc/fstab`
        
    - 3.1.2 `pacstrap -K /mnt base linux linux-firmware sudo vim`
        
    - 3.1.3 `arch-chroot /mnt`
        
- **3.2 System Configuration**
    
    - 3.2.1 Timezone: `timedatectl set-timezone <Region/City>`
        
    - 3.2.2 Sync Hardware Clock: `hwclock --systohc`
        
    - 3.2.3 Locale: Uncomment `en_US.UTF-8 UTF-8` in `/etc/locale.gen`, then run `locale-gen`.
        
    - 3.2.4 Configs: `echo "LANG=en_US.UTF-8" > /etc/locale.conf`
        
    - 3.2.5 Identity: Set `echo "arch" > /etc/hostname`.
        

### 4. System Optimization and Bootloader

- **4.1 User Management**
    
    - 4.1.1 Set root password: `passwd`
        
    - 4.1.2 Create user: `useradd -m <username>` and `passwd <username>`
        
- **4.2 Memory Optimization (ZRAM)**
    
    - 4.2.1 `pacman -S zram-generator`
        
    - 4.2.2 Configure `/etc/systemd/zram-generator.conf`:
        
        Ini, TOML
        
        ```
        [zram0]
        zram-size = ram / 2
        compression-algorithm = zstd
        swap-priority = 100
        ```
        
- **4.3 Bootloader**
    
    - 4.3.1 `pacman -S grub efibootmgr`
        
    - 4.3.2 `mkdir -p /boot/efi && mount /dev/sda1 /boot/efi`
        
    - 4.3.3 `grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=GRUB`
        
    - 4.3.4 `grub-mkconfig -o /boot/grub/grub.cfg`
        

### 5. Desktop and Package Management

- **5.1 Environment Setup**
    
    - 5.1.1 `pacman -S networkmanager sddm gnome base-devel git`
        
    - 5.1.2 `systemctl enable NetworkManager`
        
    - 5.1.3 `systemctl enable sddm`
        
- **5.2 AUR Helper (Yay)**
    
    - 5.2.1 Switch to user: `su - <username>`
        
    - 5.2.2 Install: `git clone https://aur.archlinux.org/yay.git && cd yay && makepkg -si`
        

### 6. Finalization

- **6.1 Cleanup**
    
    - 6.1.1 Exit chroot: `exit`
        
    - 6.1.2 Unmount: `umount -R /mnt`
        
    - 6.1.3 Reboot: `reboot` (Remove USB media)



7. cfdisk /dev/sda
8. gpt
9. New create 500Mb for sda1
10. New create the rest for sda2
11. formatting
	1. mkfs.fat -F 32 /dev/sda1
	2. mkfs.btrfs -f /dev/sda2
12. mounting
	1. mount /dev/sda2 /mnt
	2. btrfs subvolume create /mnt/@home
	3. btrfs subvolume create /mnt/@snapshots
	4. btrfs subvolume create /mnt/@var_log
13. umount /mnt
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