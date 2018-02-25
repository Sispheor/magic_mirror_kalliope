# Kalliope and Magic Mirror

## Used hardware

- XH-M189 2X50W Digital Amplifier
- Raspberry Pi 3
- Respeaker Mic Array


## Rpi configuration

Once Raspbian Strech lite is installed.

### SSH

Active SSH
```
sudo systemctl enable ssh
sudo systemctl start ssh
```

From here you can connect remotly to the Rpi.

### Force full screen

If you don't have a full screen, disable the overscan int `/boot/config.txt`
```
disable_overscan=1
```

### Wifi

Install a package to set Wifi automatically
```

```

## Audio configuration

In this documentation, the speaker is a Respeaker Mic Array.

We want that the
- output audio (what Kalliope says) goes on the RPi analog audio
- input audio (what we say to Kalliope) on the USB microphone (ReSpeaker)


Create a file `/etc/asound.conf` with the content bellow
```
pcm.!default {
   type asym
   playback.pcm {
     type plug
     slave.pcm "hw:0,0"
   }
   capture.pcm {
     type plug
     slave.pcm "hw:1,0"
   }
}
```

Restart alsa
```
sudo /etc/init.d/alsa-utils restart
```

To ensure voice recording, run the following command to capture audio input from the microphone:
```
rec test.wav
```
Press CTRL-C after capturing a voice sample.

Play the recorded audio file:
```
mplayer test.wav
```

## Kalliope installation

Follow the [official documentation](https://github.com/kalliope-project/kalliope/blob/master/Docs/installation.md).


Create log folder
```
sudo mkdir /var/log/kalliope
sudo chown pi: /var/log/kalliope
```

To start Kalliope automatically on startup, create a file `/etc/systemd/system/kalliope.service`.
Replace the line `/home/pi/my_kalliope_config/` by the real path of your Kalliope configuration.

```
[Unit]
Description=Kalliope

[Service]
WorkingDirectory=/home/pi/my_kalliope_config/

Environment='STDOUT=/var/log/kalliope.log'
Environment='STDERR=/var/log/kalliope.err.log'
ExecStart=/bin/bash -c "/usr/local/bin/kalliope start > ${STDOUT} 2> ${STDERR}"
User=%i

[Install]
WantedBy=multi-user.target
```

## MagicMirror installation

### Install desktop envirnoment
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

Uninstall pulseaudio which has been installed durring the process. This last will break the audio configuration handled by Alsa.
```
sudo apt-get autoremove --purge pulseaudio
```

Reboot to take care of changes
```
sudo reboot
```

### Install MagicMirror app
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

### Disable the screensaver

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

### Auto starting MagicMirror


Install PM2 using NPM:
```
sudo npm install -g pm2
```

Starting PM2 on Boot:
```
pm2 startup
```

PM2 will now show you a command you need to execute.

The command should be this one:
```
sudo su -c "env PATH=$PATH:/usr/bin pm2 startup linux -u pi --hp /home/pi"
```