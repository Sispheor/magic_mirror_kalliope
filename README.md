# Kalliope and Magic Mirror

## Used hardware

- XH-M189 2X50W Digital Amplifier
- Raspberry Pi 3
- Respeaker Mic Array


## Rpi configuration

Once Raspbian Strech lite is installed.

Active SSH
```
sudo systemctl enable ssh
sudo systemctl start ssh
```

If you don't have a full screen, disable the overscan int `/boot/config.txt`
```
disable_overscan=1
```

Reboot the Pi to take care of changes.


## Kalliope installation

Follow the [official documentation](https://github.com/kalliope-project/kalliope/blob/master/Docs/installation.md).

## Magic Mirror installation

Install Xserver, LXDE-gui and lightdm
```
sudo apt-get install xinit xserver-xorg lxde-core lightdm
```

Let the LXDE-gui autostart:
```
sudo raspi-config
```

Go to "Boot Options" and change boot to "Desktop" or "Desktop Autologin"

Install necessary packages
```
sudo apt-get install git libxss1 libnss3
```

Autohiding the Mouse Cursor with unclutter:
```
sudo apt-get install unclutter
```

Reboot to take care of changes
```
sudo reboot
```

Get and install MagicMirror with the Automatic Installer:
```
curl -sL https://raw.githubusercontent.com/MichMich/MagicMirror/master/installers/raspberry.sh | bash
```

Go to MagicMirror folder:
```
cd ~/MagicMirror
```

Install the app:
```
npm install
```

If you are directly on the Pi (not SSH), use the command bellow to test the Magic Mirror app.
```
npm start
```

If you are using SSH, use this command instead
```
DISPLAY=:0 nohup npm start &
```

## Disable the screensaver

Edit `/etc/xdg/lxsession/LXDE/autostart` and `.config/lxsession/LXDE/autostart`

Comment out or remove the line which concerne the screensaver:
```
@xscreensaver -no-splash
```

And add the following lines:
```
@xset s noblank @xset s off @xset -dpms
```

Edit `/etc/lightdm/lightdm.conf`. And add this in the section **[Seat:*]**:
```
xserver-command=X -s 0 -dpms
```

Reboot the RPi
```
sudo reboot
```