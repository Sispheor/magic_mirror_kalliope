# Kalliope

- [Kalliope](#kalliope)
    - [Installation](#installation)
    - [Add kalliope to the startup](#add-kalliope-to-the-startup)

## Installation
Installation with script
```
bash -c "$(curl -sL https://raw.githubusercontent.com/kalliope-project/kalliope/master/install/rpi_install_kalliope.sh)"
```

Create log folder and give right to the Pi user on it
```
sudo mkdir /var/log/kalliope
sudo chown pi: /var/log/kalliope
```

## Add kalliope to the startup

To start Kalliope automatically on startup, create a file `/etc/systemd/system/kalliope.service`.
Replace the line `/home/pi/my_kalliope_config/` by the real path of your Kalliope configuration.

```
[Unit]
Description=Kalliope

[Service]
WorkingDirectory=/home/pi/my_kalliope_config/

Environment='STDOUT=/var/log/kalliope.log'
Environment='STDERR=/var/log/kalliope.err.log'
ExecStart=/bin/bash -c "/usr/local/bin/kalliope start --debug > ${STDOUT} 2> ${STDERR}"
User=pi

[Install]
WantedBy=multi-user.target
```

Then active the service on startup
```
sudo systemctl enable kalliope
sudo systemctl start kalliope
```
