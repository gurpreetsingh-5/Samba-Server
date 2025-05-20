# Samba-Server
```
For Server---

sudo dnf install samba samba-common samba-client
sudo mkdir -p /samba/share
sudo chmod -R 0777 /samba/share
sudo chown -R nobody:nobody /samba/share
sudo cp /etc/samba/smb.conf /etc/samba/smb.conf.bak
sudo vi /etc/samba/smb.conf
[shared]
   comment = Samba Share
   path = /samba/share
   browsable = yes
   writable = yes
   guest ok = yes
   read only = no
   create mask = 0777
   directory mask = 0777
sudo firewall-cmd --permanent --add-service=samba
sudo firewall-cmd --reload
sudo systemctl enable smb --now
sudo systemctl enable nmb --now
sudo systemctl restart smb nmb

```
