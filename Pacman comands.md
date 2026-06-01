Pacman Commands
View mirror configuration: cat /etc/pacman.d/mirrorlist

Remove package: sudo pacman -R [package]

Install package: sudo pacman -S [package]

Find orphan packages: pacman -Qdt

Synchronize package database: sudo pacman -Syy

Synchronize and upgrade system: sudo pacman -Syu

Updating the Mirrorlist
Generate your list from the official Arch Linux mirrorlist generator (https://archlinux.org/mirrorlist/) by selecting your country and enabling https and ipv4.

Navigate to the directory: cd /etc/pacman.d

List the files to confirm: ls

Create a backup: sudo cp mirrorlist mirrorlist.bak

Clear the file: sudo truncate -s 0 mirrorlist

Edit the file: sudo nano mirrorlist

Paste your generated list into the editor and save it.

Refresh the database: sudo pacman -Syy

