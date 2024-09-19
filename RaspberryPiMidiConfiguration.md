# Raspberry Pi MIDI Configuration

These are the steps to enable MIDI in/out in the background for applications such as software synthesizers or GrandOrgue.

https://www.osaelectronics.com/learn/setting-up-raspberry-pi-for-midi/

## Remove baud setting from boot command

`sudo nano /boot/firmware/cmdline.txt`

Remove `console=serial0,115200`

https://youtu.be/RbdNczYovHQ?t=304

## Add kernel overlays to config.txt

`sudo nano /boot/firmware/config.txt`

Add:
```
enable_uart=1
dtoverlay=pi3-miniuart-bt
dtoverlay=midi-uart0
```

https://youtu.be/RbdNczYovHQ?t=372


## Disable serial console

`sudo raspi-config`

Interfacing options -> P6 Serial -> Disable login shell -> Enable serial port

## Reboot

## Install TTYMIDI

```
sudo apt-get install libasound2-dev

wget http://www.varal.org/ttymidi/ttymidi.tar.gz
tar -zxvf ttymidi.tar.gz
cd ttymidi/
make
sudo make install
```

`ttymidi -s /dev/ttyAMA0 -b 38400  &`

Will run ttymidi in the back ground.


`ttymidi -s /dev/ttyAMA0 -b 38400  -v`

Will run ttymidi in verbose mode (midi traffic displayed on screen).

https://siliconstuff.blogspot.com/2012/08/ttymidi-on-raspberry-pi.html

## Add TTYMIDI as a service

`nano ttymidi.sh`

```
#/bin/bash

ttymidi -s /dev/ttyAMA0 -b 38400
```

`chmod 777 ttymidi.sh`

`sudo nano /lib/systemd/system/ttymidi.service`

```
[Unit]
Description=Run ttymidi as a background service
After=network-online.target

[Service]
ExecStart=/bin/bash /home/aidan/ttymidi.sh
WorkingDirectory=/home/aidan/
StandardOutput=inherit
StandardError=inherit
Restart=always
User=aidan

[Install]
WantedBy=multi-user.target
```

`sudo systemctl enable ttymidi.service`

`sudo service ttymidi status/start/stop`

https://domoticproject.com/creating-raspberry-pi-service/
