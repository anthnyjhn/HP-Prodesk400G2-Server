# MY HARDWARE SPECS

- Intel i5-4590 3.70GHZ
- 12GB RAM
- 128GB SSD - Boot Drive
- 1TB HDD - Storage Drive

# DOWNLOAD OS

Download Ubuntu Server LTS from https://ubuntu.com/download/server.
Flash your OS to a flash drive. I used **Balena Etcher** but you can use almost any OS flashing softwares.

# SETUP BIOS

Boot Order - set the flash drive as the first boot option.

# INSTALL OS

Install Ubuntu Server to your server and follow the prompt. It should be self-explanatory.

# SETUP OS

## Update packages:

To update your repo and get the latest updates

```bash
sudo apt update && sudo apt upgrade
```

## Get IP & MAC address:

Write your MAC address. It's going to be useful in the future.

```bash
ip link
```

## Check SSH and Enable:

Enable ssh for remote access.

```bash
    systemctl status ssh
    systemctl enable ssh
```

## Reserve an IP address on Router to your device's MAC address

Go to your router's control panel. Reserve a static ip to your Server's MAC address.

## Disable Graphical Terminal (boot without display)

```bash
nano /etc/default/grub

GRUB_CMDLINE_LINUX="text" # Find and add 'text' to this line
GRUB_TERMINAL=console # uncomment this line

```

## Update grub:

```bash
sudo update-grub
```

## Disable .TTY1 (Auto login)

```bash
sudo mkdir -p /etc/systemd/system/getty@tty1.service.d/
sudo nano /etc/systemd/system/getty@tty1.service.d/override.conf
```

Paste:

```bash
[Service]
ExecStart=
ExecStart=-/sbin/agetty --autologin YOUR_USERNAME --noclear %I $TERM
```

### Reload systemd and enable auto-login

```bash
    sudo systemctl daemon-reexec
    sudo systemctl restart getty@tty1
```

## Setup CasaOS

Install curl:

```bash
sudo apt install -y curl
```

Install casaOS:

```bash
curl -fsSL https://get.casaos.io | sudo bash
```

If installation failed (Docker):
Remove docker list:

```bash
sudo rm -f /etc/apt/sources.list.d/docker.list
sudo apt update
```

Install Docker manually (official method):

```bash
sudo apt update
sudo apt install -y docker.io docker-compose
```

## Setup Shared Folder

**Using CasaOS**
Create Storage (1TB)
Storage Manager > Create Storage

Create Shared Storage
Files > `/mnt/<$DriveName>`
Create a Folder and Tick "Shared"
Get link:
Files > Shared > Right click on Folder and Get Network Path

or

**Create Manually**

```bash
sudo mkdir -p /srv/samba/FOLDERNAME
```

Edit Samba config

```bash
sudo nano /etc/samba/smb.conf
```

```bash
[nas] # 'nas' will appear in the smb url. E.g., smb://192.168.1.199/nas
path = /srv/samba/FOLDERNAME
vfs objects = catia fruit streams_xattr # uncomment this line

# paste these lines
read only = no
browsable = yes
guest ok = no
```

restart samba and net bios

```bash
sudo systemctl restart smbd nmbd
```

Add samba users:

```bash
sudo adduser YOUR_USERNAME # create server user
sudo smbpasswd -a YOUR_USERNAME # add user account to samba and set password
sudo smbpasswd -e YOUR_USERNAME # enable user account

sudo chown YOUR_USERNAME:YOUR_USERNAME /srv/samba/FOLDERNAME # Set user permission
sudo chmod 770 /srv/samba/FOLDERNAME
```

Access smb:

```bash
smb://IPADDRESS/FOLDERNAME
```

Pi-hole on CasaOS Setup:
Clear Port 53, Install pi-hole by pi-hole via casaOS apps store, give <$portNumber>:80
https://youtu.be/uOvB0CIhb5k?si=nWvAG3qImAG5oeWJ

Ollama + Docker + OpenWeb UI (Bellulama)
https://github.com/ai-joe-git/Belullama

# Power Efficiency

### **Enable Powertop Autotune**

Massive improvement for idle power.

```bash
sudo apt install powertop sudo powertop --auto-tune
sudo nano /etc/systemd/system/powertop.service
```

Paste:

```bash
[Unit]
Description=Powertop tunings
[Service]
Type=oneshot
ExecStart=/usr/sbin/powertop --auto-tune
[Install]
WantedBy=multi-user.target
```

Enable powertop:

```bash
sudo systemctl enable --now powertop.service
```

## Reduce Disk Power Usage

Install hdparm

```bash
sudo apt install hdparm
lsblk # get drive name

sudo hdparm -B 1 -S 120 /dev/<$drivename> # apply hdparm
```

- `B 1` = minimum power
- `S 12` = spin down after 1 minute idle
