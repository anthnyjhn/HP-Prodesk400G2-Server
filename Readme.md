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

    `sudo apt update && sudo apt upgrade`

## Get IP & MAC address:

    `ip link`

## Check SSH and Enable:

    `systemctl status ssh`
    `systemctl enable ssh`

## Reserve an IP address on Router to your device's MAC address

## Disable Graphical Terminal (boot without display)

    `nano /etc/default/grub`
    Change:
    	`GRUB_CMDLINE_LINUX="" to GRUB_CMDLINE_LINUX="text"`
    	uncomment:  `GRUB_TERMINAL=console`

## Update grub:

`sudo update-grub`

## Disable .TTY1 (Auto login)

    `sudo mkdir -p /etc/systemd/system/getty@tty1.service.d/`
    `sudo nano /etc/systemd/system/getty@tty1.service.d/override.conf`

Paste:
`[Service]`
`ExecStart=`
`ExecStart=-/sbin/agetty --autologin <your-username> --noclear %I $TERM`

### Reload systemd and enable auto-login

    `sudo systemctl daemon-reexec`
    `sudo systemctl restart getty@tty1`

## Setup CasaOS

Install curl:
`sudo apt install -y curl`

Install casaOS:
`curl -fsSL https://get.casaos.io | sudo bash`

If installation failed (Docker):
Remove docker list:
`sudo rm -f /etc/apt/sources.list.d/docker.list`
`sudo apt update`

Install Docker manually (official method):
`sudo apt update`
`sudo apt install -y docker.io docker-compose`

Create Storage (1TB)
Storage Manager > Create Stocker > Create

Create Shared Storage
Files > /mnt/<$DriveName>
Create a Folder and Tick "Shared"
Get link:
Files > Shared > Right click on Folder and Get Network Path

Edit Samba config
`sudo nano /etc/samba/smb.conf`
uncomment
`vfs objects = catia fruit streams_xattr`
`read only = no`
`guest ok = no`
restart samba and net bios
`sudo systemctl restart smbd nmbd`

Add samba users:
`sudo adduser <$username>`
`sudo smbpasswd -a <$username>`
`sudo smbpasswd -e <$username>`

Pi-hole on CasaOS Setup:
Clear Port 53, Install pi-hole by pi-hole via casaOS apps store, give <$portNumber>:80
https://youtu.be/uOvB0CIhb5k?si=nWvAG3qImAG5oeWJ

Ollama + Docker + OpenWeb UI (Bellulama)
https://github.com/ai-joe-git/Belullama

ConvertX:
container port: 80 - 3000 only.

# Power Efficiency

### **Enable Powertop Autotune**

Massive improvement for idle power.
`sudo apt install powertop sudo powertop --auto-tune`

Make it persistent:
`sudo nano /etc/systemd/system/powertop.service`

Paste:
`[Unit] Description=Powertop tunings  [Service] Type=oneshot ExecStart=/usr/sbin/powertop --auto-tune  [Install] WantedBy=multi-user.target`

Enable:
`sudo systemctl enable --now powertop.service`

## Reduce Disk Power Usage

Install hdparm
`sudo apt install hdparm`

Get drive name:
`lsblk`

Apply:
`sudo hdparm -B 1 -S 120 /dev/<$drivename>`

- `B 1` = minimum power
- `S 12` = spin down after 1 minute idle
