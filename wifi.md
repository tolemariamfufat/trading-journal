via 🦀    
❯ # 1. Tell NetworkManager to ignore the router's DNS on your Wi-Fi  
sudo nmcli connection modify id "$(nmcli -t -f NAME,TYPE connection show --active |  
grep wireless | cut -d: -f1)" ipv4.ignore-auto-dns yes  
  
# 2. Add the custom DNS directly to the Wi-Fi connection interface  
sudo nmcli connection modify id "$(nmcli -t -f NAME,TYPE connection show --active |  
grep wireless | cut -d: -f1)" ipv4.dns "1.1.1.1 8.8.8.8"  
  
# 3. Restart the Wi-Fi connection to apply the changes  
sudo nmcli connection up id "$(nmcli -t -f NAME,TYPE connection show --active | grep  
wireless | cut -d: -f1)"

sudo nano /etc/systemd/resolved.conf

sudo systemctl restart systemd-resolved


resolvectl status wlo1