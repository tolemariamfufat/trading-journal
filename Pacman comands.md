cat /etc/pacman.d/mirrorlist
sudo pacman -R [package] (remove)
sudo pacman -S [package]
pacman -Qdt (find orphan packages)
sudo pacman -Syy (sink pakage manager and locale packages)
sudo pacman -Syu (upgrade packages which are upgradable)

update mirror list
https://www.archlinux.org/mirrorlist
then choose your country and select only https and ipv4 mirror status then genrate the list then copy paste on cat /etc/mirrorlist
cd /etc/pacman.d
ls
cp mirrorlist mirrorlist.bak
sudo truncate -s 0 mirrorlist
sudo nano mirrorlist
paste the generated mirrorlist
sudo pacman -Syy
