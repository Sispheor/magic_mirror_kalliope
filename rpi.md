# Rpi configuration

- [Rpi configuration](#rpi-configuration)
    - [SSH](#ssh)
    - [Force full screen](#force-full-screen)
    - [Timezone](#timezone)
    - [Wifi](#wifi)
    - [Audio configuration](#audio-configuration)

Following configuration has been tested on Raspbian Strech lite.

### SSH

Active SSH
```
sudo systemctl enable ssh
sudo systemctl start ssh
```

From here you can connect remotely to the Rpi.

### Force full screen

If you don't have a full screen, disable the overscan int `/boot/config.txt`
```
disable_overscan=1
```

### Timezone

Run the Raspberry configuration software.
```
sudo raspi-config
```

Go inside Timezone menu and set the right timezone corresponding to your current location. e.g. Europe, then selecting a city, e.g. Paris.

You can then check that the current time is correct by typing
```
date
```

### Wifi

The main idea when creating a Magic Mirror is to limit the final number of wire to one, the one used to provide power supply.

Next commands can be retrieved with a complete explanation from the [official Raspberry Pi website(https://www.raspberrypi.org/documentation/configuration/wireless/wireless-cli.md)].

Edit `/etc/wpa_supplicant/wpa_supplicant.conf` and add your config to the end of the file. E.g:
```
network={
    ssid="testing"
    psk="testingPassword"
}
```

Restart the network service
```
sudo systemctl restart networking
```

Check that the Rpi now has an IP on its WLAN interface
```
ip a
```

Output example:
```
3: wlan0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether b8:27:eb:b1:8d:e6 brd ff:ff:ff:ff:ff:ff
    inet 192.168.0.28/24 brd 192.168.0.255 scope global wlan0
       valid_lft forever preferred_lft forever
    inet6 fe80::6d72:7d72:b7c4:f815/64 scope link
       valid_lft forever preferred_lft forever
```

Enable the wifi on startup
```
sudo systemctl enable wpa_supplicant.service
```

Reboot the RPI to check that the wifi auto connect on startup
```
sudo reboot
```

### Audio configuration

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
