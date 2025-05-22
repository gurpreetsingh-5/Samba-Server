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

\\<server-site-ip>  # in Windows click search and type then enter


For client---

sudo dnf install samba-client cifs-utils -y
mkdir -p /mnt/samba/apps
sudo mount -t cifs //server-ip/Apps /mnt/samba/apps
df -h

Secure Samba server---
(in server site)

groupadd smbgrp
useradd -M -d /samba_secure -s /usr/sbin/nologin -G smbgrp sambauser
id sambauser
mkdir /samba_secure
ls -ltr
chown sambauser:smbgrp samba_secure
chmod 2770 samba_secure
chcon -t samba_share_t /samba_secure
smbpasswd -a sambauser
smbpasswd -e sambauser
vi /ete/samba/smb.conf
[Secure]
        path = /samba_secure
        valid users = @smbgrp
        guest pk = no
        writable = yes
        browsable = yes

systemctl restart smb nmb
systemctl status smb nmb

Foe Windows--
\\<server-site-ip>\Secure  # in Windows click search and type then enter
username:-----
password:-----

for redhat---
sudo mkdir -p /mnt/samba/secure
sudo nano /etc/samba/secure_credentials
username=sambauser
password=yourpassword
sudo chmod 600 /etc/samba/secure_credentials
sudo mount -t cifs //server-ip/Secure /mnt/samba/secure -o credentials=/etc/samba/secure_credentials,uid=$(id -u),gid=$(id -g),dir_mode=0770,file_mode=0770
df -h
ls -l /mnt/samba/secure

For Permanent Mount (optional)
Add to /etc/fstab:
//server-ip/Secure  /mnt/samba/secure  cifs  credentials=/etc/samba/secure_credentials,uid=$(id -u),gid=$(id -g),dir_mode=0770,file_mode=0770  0  0
sudo mount -a


```
