# BUYING

- Intel i5-4590 3.70GHZ
- 12GB RAM
- 128GB SSD - Boot Drive
- 1TB HDD - Storage Drive

# SETUP

- Add SSD
- Setup BIOS
  - Boot Order
  - UEFI / Legacy

# INSTALL OS

- Download OS: Ubuntu Server 24.04 Server LTS
- USB Etcher: Balena Etcher

# SETUP OS

## Update packages:

    ```bash

sudo apt update && sudo apt upgrade`

````

## Get IP & MAC address:

 ```bash
 ip link
````

## Check SSH and Enable:

    ```bash
    systemctl status ssh
    systemctl enable ssh
    ```

## Reserve an IP address on Router to your device's MAC address

## Disable Graphical Terminal (boot without display)

```bash
nano /etc/default/grub

GRUB_CMDLINE_LINUX="text" # Change this line
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

````bash
[Service]
ExecStart=
ExecStart=-/sbin/agetty --autologin <your-username> --noclear %I $TERM```

### Reload systemd and enable auto-login

```bash
    sudo systemctl daemon-reexec
    sudo systemctl restart getty@tty1
````

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

Create Storage (1TB)
Storage Manager > Create Storage

Create Shared Storage
Files > `/mnt/<$DriveName>`
Create a Folder and Tick "Shared"
Get link:
Files > Shared > Right click on Folder and Get Network Path

Edit Samba config

```bash
sudo nano /etc/samba/smb.conf

```

```bash
vfs objects = catia fruit streams_xattr # uncomment this line

# paste these lines
read only = no
guest ok = no
```

restart samba and net bios

```bash
sudo systemctl restart smbd nmbd
```

Add samba users:

```bash
sudo adduser <$username> # create server user
sudo smbpasswd -a <$username> # add user account to samba and set password
sudo smbpasswd -e <$username> # enable user account
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
