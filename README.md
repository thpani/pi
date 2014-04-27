Raspberry Pi setup routine
==========================

## Headless boot to SSH over WiFi

Prepare `/etc/network/interfaces` on the SD card:

    auto lo
    auto eth0
    allow-hotplug wlan0
    
    iface lo inet loopback
    
    iface eth0 inet dhcp
    
    iface wlan0 inet dhcp
    wpa-ssid pani
    wpa-psk "********"
    
    iface default inet dhcp

## Basic Setup

### Change the pi user password

    (local) $ ssh-copy-id pi@<raspberry-ip>
    (local) $ ssh pi@<raspberry-ip>
    (pi)    $ passwd

### Configure and reboot (!!!)

    (pi)    $ sudo raspi-config
    (pi)    $ sudo reboot       # filesystem enlargement
    (pi)    $ sudo apt-get install python-apt

### Run ansible play

    (local) $ cp secret.yml.example secret.yml
    (local) $ $EDITOR secret.yml
    (local) $ ansible-playbook playbook.yml

## Disable power saving on the 8192CU (Edimax WiFi dongle)

`/etc/modprobe.d/8192cu.conf`

    options 8192cu rtw_power_mgnt=0 rtw_enusbss=0

## Time Machine

    $ sudo aptitude install netatalk exfat
    $ sudo blkid
    $ sudo mkdir /mnt/tm
    
`/etc/fstab`

    proc            /proc           proc    defaults          0       0
    /dev/mmcblk0p1  /boot           vfat    defaults          0       2
    /dev/mmcblk0p2  /               ext4    defaults,noatime  0       1
    UUID="4D02-8E53" /mnt/tm exfat defaults,nofail,gid=100,dmask=0002,fmask=0113 0 2
    
## Air Print

    $ sudo aptitude install avahi-daemon cups cups-pdf python-cups
    
`/etc/cups/cupsd.conf`

    [...]
    Listen *:631
    [...]
    Allow from @LOCAL
    [...]

    $ sudo service cups restart
    $ sudo adduser thomas lpadmin
    
* Browse to `https://raspberrypi:631/admin`, add the printer and share it.
* For HP printers, choose the `hpcups` driver.
