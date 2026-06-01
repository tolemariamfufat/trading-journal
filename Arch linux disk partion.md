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
Here is the "map" of what that teacher was creating and why each piece is vital:

1. The EFI Partition (usually 512MB to 1GB)
Purpose: This is the bridge between your motherboard's firmware (UEFI) and the Linux kernel. It is formatted as FAT32.

Why it's essential: Without this, your computer wouldn't know how to "find" the bootloader (like GRUB or systemd-boot) that starts your operating system.

Mount point: /boot/efi or /efi.

2. The /boot Partition (usually 500MB to 2GB)
Purpose: This holds the actual Linux kernel files and the initramfs (the initial image loaded into memory to get the system running).

Why it's essential: By putting /boot on its own partition, you protect your boot files from being affected by issues that might occur in your main system partition. It also makes it easier to use advanced features like full-disk encryption (LUKS) later on.

Mount point: /boot.

3. The Root (/) Partition (The rest of the space)
Purpose: This is where the entire operating system, your applications, and all your files live.

Why it's essential: This is your "home base." You format this as a Linux-native file system (usually ext4, or sometimes btrfs if you want features like snapshots).

Mount point: / (the root directory).
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

Partiion 1=+1G; partion 2 = +1G and particion 3 default the rest for root and then w (write, the point of no return bcs the content is erased)
The Breakdown of mkfs.fat -F 32 /dev/nvme0n1p1
When the teacher ran that command, they were preparing the EFI Partition.

mkfs: Short for "make filesystem."

.fat: Specifies the FAT (File Allocation Table) filesystem.

-F 32: Specifies FAT32. This is the universal standard for UEFI firmware. Your motherboard’s BIOS/UEFI is very simple—it doesn't understand advanced Linux filesystems like ext4 or btrfs, but it does understand FAT32.

/dev/nvme0n1p1: The target partition you created earlier.

Why we use different formats
You will likely see the teacher use different commands for the other partitions. Here is why:

For the EFI Partition (/dev/nvme0n1p1):

Command: mkfs.fat -F 32

Reason: Compatibility. The UEFI firmware requires this to find your boot files.

For the Boot/Root Partitions (/dev/nvme0n1p2, p3):

Command: mkfs.ext4 /dev/nvme0n1p2 (or mkfs.btrfs)

Reason: These are "Linux-native" filesystems. They support features that FAT32 does not, such as file permissions (who owns a file), symbolic links, and journaling (which prevents data corruption if the power cuts out).
By running cryptsetup luksFormat /dev/nvme0n1p3, they aren't just formatting the partition; they are sealing it behind a cryptographic wall.

What is happening here?
cryptsetup: This is the standard utility for managing Linux kernel-based disk encryption.

luksFormat: This creates a LUKS (Linux Unified Key Setup) header at the beginning of the partition. It will prompt you to type a passphrase. From this moment on, that partition is essentially "scrambled" noise. The computer cannot read any files on it until the correct password is provided to "unlock" it.

The Workflow of an Encrypted System
When you use encryption, the boot process changes slightly. Here is the order of operations your computer now has to follow:

UEFI/BIOS: Boots the tiny kernel from the unencrypted /boot partition.

Passphrase Request: The kernel stops the boot process and asks you for your passphrase.

Unlock: cryptsetup uses that passphrase to decrypt the master key.

Mount: Now that the "noise" is decrypted into readable data, the system mounts your root (/) filesystem and continues booting.

Why do this?
Security: If your laptop is stolen, the thief has your hardware but absolutely no way to access your personal files. Without that password, the data on /dev/nvme0n1p3 is mathematically indistinguishable from random bits of data.

Privacy: It protects your documents, browser history, and saved passwords even if someone removes the drive and tries to read it on another computer.

The "Need to Know" for your future install
If you choose to do this on your future manual installations, remember that encryption adds complexity. You will have to configure a "keyfile" or specific kernel parameters to tell the system where the encrypted partition is during boot. If you forget your passphrase, your data is gone forever—there is no "forgot password" button for LUKS.

The Standard Formatting Commands
Assuming your third partition is /dev/nvme0n1p3, here is how you would format it using the most common and robust filesystem, ext4:

Bash
mkfs.ext4 /dev/nvme0n1p3
Why ext4 is a great choice:
Reliability: It is the "tried and true" filesystem for Linux. It is exceptionally stable and has been used on millions of systems for years.

Compatibility: Every Linux tool and every kernel understands it perfectly.

Performance: It is highly efficient for general desktop use, which fits the Arch philosophy of a clean, performant system.




