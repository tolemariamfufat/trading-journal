To help you solidify your mastery, here is the list of Btrfs commands, incorporating the specific subvolume structure we used in our "dynamic" setup yesterday.

### 1. Filesystem Information & Status

- **Show filesystem details:** `sudo btrfs filesystem show /`
    
- **Show space usage:** `sudo btrfs filesystem usage /`
    
- **Check disk usage (summary):** `sudo btrfs filesystem df /`
    
- **View device statistics:** `sudo btrfs device stats /`
    

### 2. Subvolume Management (Reflecting yesterday’s setup)

- **Create a subvolume:** `sudo btrfs subvolume create /mnt/@`
    
    - _Yesterday's Example:_ We created `@` for the root and `@home` for the home directory.
        
- **List subvolumes:** `sudo btrfs subvolume list /mnt`
    
    - _Yesterday's Example:_ This command would show your `@` and `@home` subvolumes residing on the root partition.
        
- **Delete a subvolume:** `sudo btrfs subvolume delete /mnt/@home`
    
- **Create a snapshot:** `sudo btrfs subvolume snapshot /mnt/@ /mnt/@.snapshot`
    
    - _Why use this?_ This is the "time machine" feature. You could snapshot your system before a big update, so if something breaks, you can instantly roll back to that state.
        

### 3. Maintenance & Performance

- **Scrub (Data Verification):** `sudo btrfs scrub start /`
    
    - _Why use this?_ Btrfs is self-healing. Scrubbing tells the system to check every bit of data against its checksums and fix any silent corruption automatically.
        
- **Check scrub status:** `sudo btrfs scrub status /`
    
- **Balance (Performance Optimization):** `sudo btrfs balance start /`
    
    - _Note:_ Only run this if your disk is getting very full or if you are adding/removing devices.
        

### 4. Device Management

- **Add a device:** `sudo btrfs device add /dev/sdb1 /mnt`
    
    - _Context:_ Btrfs allows you to add a whole new hard drive to your existing pool at any time, expanding your storage space without reformatting.
        
- **Scan for devices:** `sudo btrfs device scan`
    

### 5. Troubleshooting

- **Check/Repair filesystem:** `sudo btrfs check /dev/nvme0n1p2`
    
    - _Warning:_ Run this only when the filesystem is unmounted.
        

### The Recovery Process (Steps)

1. **Mount the root of the Btrfs partition:** You cannot rename or move subvolumes while they are mounted as your active system. Boot from your live installation USB, then mount the actual partition (the one containing your subvolumes).
    
    Bash
    
    ```
    sudo mount /dev/nvme0n1p2 /mnt
    ```
    
2. **Rename (or "hide") your current broken state:** Rename your current, problematic root subvolume so the system can't see it as the primary one.
    
    Bash
    
    ```
    sudo mv /mnt/@ /mnt/@.broken
    ```
    
3. **Restore the snapshot:** Rename the snapshot you took yesterday back to the name your bootloader is looking for.
    
    Bash
    
    ```
    sudo mv /mnt/@.morning_snapshot /mnt/@
    ```
    
4. **Verify:** List them to ensure `@` is back to where it should be.
    
    Bash
    
    ```
    sudo btrfs subvolume list /mnt
    ```
    
5. **Reboot:** Unmount and reboot. Your system will now boot into the state it was in yesterday morning.
    
    Bash
    
    ```
    sudo umount /mnt
    reboot
    ```
    

### A Crucial Note on "Moving" vs. "Snapshotting"

Btrfs subvolumes are like pointers. When you rename `@.morning_snapshot` to `@`, you are simply updating the pointer that the system uses to identify "where the root is."

**What if you want to keep working on the current state but just copy files?** If you don't want to revert the whole system, but just need a specific file from yesterday, you don't need to move subvolumes. You can simply mount the snapshot to a temporary directory:

Bash

```
sudo mount -o subvol=@.morning_snapshot /dev/nvme0n1p2 /mnt/temp
```

Then you can just `cp /mnt/temp/etc/some_file /etc/` to grab the file you lost without reverting your entire OS.