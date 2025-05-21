# Samba-Server
```
For Server---

sudo dnf install samba samba-common samba-client
sudo systemctl status firewalld
sudo dnf install firewalld # if not present
sudo firewall-cmd --permanent --add-service=samba
sudo firewall-cmd --reload
sudo systemctl status firewalld

sudo mkdir -p /samba/apps
sudo chmod -R 0777 /samba/apps
sudo chmod -R 0777 /samba/apps
sudo chmod -R 0777 /samba/apps/*

chcon -t samba_share_t /samba/apps/


sudo chown -R nobody:nobody /samba/apps
sudo cp /etc/samba/smb.conf /etc/samba/smb.conf.copy
sudo vi /etc/samba/smb.conf

[global]
        workgroup = SAMBA
        security = user
        netbios name = rhel9-samba
        map to guest = bad user
        dns proxy = no
        hosts allow = 192.168.0.0/24
        #passdb backend = tdbsam
        #printing = cups
        #printcap name = cups
        #load printers = yes
        #cups options = raw

[Apps]
        comment = Samba Dir
        path = /samba/apps
        browsable = yes
        writable = yes
        guest ok = yes
        guest only = yes
        read only = no

testparm

systemctl start smb nmb
systemctl status smb nmb



[shared]
   comment = Samba Share
   path = /samba/share
   browsable = yes
   writable = yes
   guest ok = yes
   read only = no
   create mask = 0777
   directory mask = 0777

[global]
    workgroup = SAMBA
    server string = Samba Server %v
    netbios name = rhel9-samba
    security = user
    map to guest = bad user
    dns proxy = no

[shared]
    path = /samba/share
    browsable = yes
    writable = yes
    guest ok = no
    read only = no
    create mask = 0777



testparm
sudo systemctl enable smb --now
sudo systemctl enable nmb --now
sudo systemctl restart smb nmb
sudo systemctl status smb nmb

sudo useradd sambauser
sudo smbpasswd -a sambauser

For Client---

sudo dnf install samba-client cifs-utils
sudo dnf install samba samba-common samba-client
smbclient -L //server-ip -U username
# Mount Samba Share Temporarily
sudo mkdir /mnt/sambashare
sudo mount -t cifs //server-ip/shared /mnt/sambashare -o username=sambauser
# Mount Samba Share Permanently (add to fstab)
sudo vi /etc/fstab
//server-ip/shared  /mnt/sambashare  cifs  username=sambauser,password=yourpassword,uid=1000,gid=100   0,vers=3.0  0  0
sudo mount -a

sudo systemctl status smb nmb

sudo chcon -t samba_share_t /samba/share  #If you have SELinux enabled, you may need to set proper contexts:
```
