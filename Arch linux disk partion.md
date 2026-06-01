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
Open the encryption: 
A Note on the Command
You noticed he typed **cryptsetup open --type luks /dev/nvme0n1p3**.

The --type luks part is a good habit—it explicitly tells the system which encryption standard to use.
The Standard Formatting Commands
Assuming your third partition is /dev/nvme0n1p3, here is how you would format it using the most common and robust filesystem, ext4:

Bash
mkfs.ext4 /dev/nvme0n1p3
Why ext4 is a great choice:
Reliability: It is the "tried and true" filesystem for Linux. It is exceptionally stable and has been used on millions of systems for years.

Compatibility: Every Linux tool and every kernel understands it perfectly.

Logical volume
pvcreate /dev/mapper/lvm
By running pvcreate, he was adding yet another layer of flexibility. When you see pvcreate, vgcreate, and lvcreate in an Arch installation, it means the user wants to be able to resize their partitions easily later on without having to repartition the physical disk.

vgcreate volgroup0 /dev/mapper/lvm

lvcreate -L 30GB volgroup0 -n lv_root
What this command means:
lvcreate: The command to carve a logical slice out of the Volume Group.

-L 30GB: He is explicitly setting the size to 30 Gigabytes. This is the "partition" that will hold your operating system.

volgroup0: The source "pool" where this space is coming from.

-n lv_root: The name he is giving this logical volume (short for "Logical Volume Root").

Why he chose 30GB:
This is a standard, safe size for a root partition. It is large enough to hold:

The Base System: The core Arch Linux OS.

Packages: All the software you install using pacman.

Logs and Libraries: The data the OS needs to run efficiently.

Note: By keeping the root partition at a fixed size (30GB) while putting data like your personal files, movies, and photos in a different logical volume (like lv_home), he ensures that if your system fills up with logs, it won't prevent you from saving your personal documents.

lvcreate -L 800GB volgroup0 -n lv_home

By creating an lv_home volume with 800GB, he is physically separating your data from your system files. This is a classic "pro" setup.

The Strategy Behind this Partitioning
System Integrity: Because lv_root (30GB) and lv_home (800GB) are separate volumes, you could completely wipe and reinstall the operating system on lv_root without ever touching your personal files in lv_home.

Safety: If you accidentally download a file that is too large or fill up your home directory with media, the system will not crash. The operating system on the root partition will continue to function perfectly because it has its own dedicated space.

activation: 
modprobe dm_mod
vgscan 
vgchange -ay
These commands are the "wake-up call" for LVM. You have defined the storage structure, but the Linux kernel doesn't automatically "see" or interact with these virtual volumes until you explicitly load the drivers and activate the volume groups.

Here is what is happening under the hood:

1. modprobe dm_mod
What it does: This loads the dm_mod (Device Mapper) kernel module.

Why: Linux is modular. LVM relies on the Device Mapper infrastructure to create that virtual mapping between your physical disk and your logical volumes (lv_root, lv_home). By running modprobe, you are telling the kernel to enable the "engine" that makes LVM work.

2. vgscan
What it does: It scans all of your disks for Physical Volumes (PVs) that belong to a Volume Group (VG).

Why: It updates the LVM cache so the system knows that volgroup0 exists and is sitting on your encrypted device. It’s like a "search" command to locate all your LVM building blocks.

3. vgchange -ay
What it does: This is the activation command. The -a flag stands for "activate" and y stands for "yes."

Why: Even if the system finds a Volume Group, it remains in an "inactive" state for safety. This command makes your Logical Volumes (lv_root, lv_home, etc.) appear in /dev/mapper/ so that you can finally format and mount them.

The Big Picture
You have now traced the entire lifecycle of an advanced Arch installation:

Preparation: fdisk (Defining boundaries)

Security: cryptsetup (The encrypted lock)

LVM Foundation: pvcreate -> vgcreate -> lvcreate (Building the storage logic)

Activation: modprobe -> vgscan -> vgchange (Bringing the storage to life)

You now have a fully prepared, encrypted, and logically partitioned environment ready for the OS to be installed. The final steps for the teacher would simply be:

Formatting: mkfs.ext4 on the logical volumes.

Mounting: mount the volumes to /mnt.

Installation: pacstrap /mnt base linux linux-firmware.

Formating: 
mkfs.ext4 /dev/volgroup0/lv_root
mkfs.ext4 /dev/volgroup0/lv_home
What is happening here?
By running mkfs.ext4 on those specific paths, you are creating the filesystem architecture on your newly created logical volumes.

mkfs.ext4: This creates an "ext4" journaled filesystem, which is the industry standard for Linux performance and reliability.

/dev/volgroup0/lv_root: This is the "path" to your logical volume. You are effectively formatting your 30GB "root" slice.

/dev/volgroup0/lv_home: You are formatting your 800GB "home" slice.

Mounting: 
mount /dev/volgroup0/lv_root /mnt
mkdir /mnt/boot
mount /dev/nvme0n1p1 /mnt/boot
mkdir /mnt/home
mount /dev/volgroup0/lv_home /mnt/home
This is the final "wiring" of your system. Think of this step as connecting all your logical volumes and partitions into one single, unified tree structure that the Linux kernel can understand as a single OS.

Without mounting, your filesystems are just "islands" floating in the void. By mounting them, you are placing them into the /mnt directory, which acts as the "staging ground" for your new Arch installation.

The Layout You Just Created
Here is how that command sequence maps your directory tree:

/mnt: This is your root (/). When the installation is finished, everything you install will live here.

/mnt/boot: This is your dedicated boot partition (/dev/nvme0n1p1). By mounting it here, you ensure that the system knows exactly where to store the Kernel and bootloader files.

/mnt/home: This is your data partition (/dev/volgroup0/lv_home). By mounting it here, you ensure that all user profiles and personal files are automatically directed to that large 800GB volume.

pacstrap -i /mnt base
What is pacstrap?
pacstrap is a special script unique to Arch Linux. It is essentially an installer that knows how to reach out to the Arch servers, download the core packages, and "strap" (install) them into the target directory (/mnt) you just set up.

-i (Interactive): This flag tells pacstrap to ask you for confirmation before installing packages. It’s a good safety check to ensure you know exactly what is being added.

/mnt: This is your target destination—the root of the new system you just mounted.

base: This is a group of essential packages. It contains the bare minimum software required to have a functioning Linux system, including the kernel, the shell (bash), and basic system tools.

The "Strap" Analogy
Think of your disk as an empty house that you’ve just finished building (the partitioning/mounting phase). pacstrap is the crew arriving with all the furniture, plumbing, and electrical components. It takes those files from the internet and places them exactly where they need to go inside the folders you created (/mnt/boot, /mnt/home, etc.).

A Typical Interaction
When the teacher ran this, the terminal likely looked like this:

Repository Sync: It checks the Arch servers to see what versions of the "base" packages are available.

Prompt: It asks, "Do you want to proceed with this installation?" (This is where the -i flag kicks in).

Download and Install: It downloads everything and starts writing files to your drive.

genfstab -U -p /mnt >> /mnt/etc/fstab

cat /mnt/etc/fstab

This is the "Memory" step. You have built the house, and now you are writing down the architectural blueprints so the system remembers where everything is located after you turn it off and turn it back on.

What is genfstab?
genfstab stands for Generate File System Table.

-U: This is crucial. It tells the system to use UUIDs (Universally Unique Identifiers) rather than device names (like /dev/nvme0n1p1). Device names can change if you add or remove drives, but a UUID is a unique "fingerprint" for that specific partition. This ensures your computer always finds the right drive, no matter what.

-p: This ensures that mount points (like your /boot and /home partitions) are included in the generated file.

/mnt: This tells the tool to look at the currently mounted system.

>> /mnt/etc/fstab: This is the redirection operator. It takes the output of the command and appends it to the fstab file inside your new system.

Why this file (/etc/fstab) is the most important file for your OS
Without this file, the next time you boot your computer, the kernel wouldn't know which partition is the root (/), which is the home directory, or where the boot files are. It would try to boot into a blank void, and the system would fail.

By running this command, you are telling the kernel:

"Whenever you start up, look at this file to find out which drive is home, which is root, and where to mount the EFI partition."

arch_chroot /mnt
passwd
useradd -m -g users -G wheel to
cat /etc/passwd
passwd to
pacman -S base-devel dosfstools grub efibootmgr gnome gnome-tweaks lvm2 mtools nano networkmanager os-prober sudo
pacman -S linux linux-headers linux-lts linux-lts-headers
