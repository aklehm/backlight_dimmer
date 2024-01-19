# Backlight dimmer
___
## Disclaimer
This a fork of the original reposity from [DougieLawson](https://github.com/DougieLawson/backlight_dimmer). His last commit is from Jun 6, 2020 and it looks like, he not working on this anymore. I encountered some problems when I tried to use this software for the official [7″ touchscreen display](https://www.raspberrypi.com/products/raspberry-pi-touch-display/). My customizations are included in this repository. The software works as is on a Raspberry Pi 3 and Raspberry Pi OS (bullseye). I have not tested it on any other hardware or operating system.
___
Leaving the backlight on the Official Raspberry Pi touchscreen can quickly wear it out.  If you have a use that requires the pi to be on all the time, but does not require the display on all the time, then dimming the backlight while not in use can dramatically increase the life of the backlight.

Backlight dimmer will transparently dim the display backlight after there has been no input for a specifed timeout, independent of anything using the display at the moment. It will then turn the touchscreen back on when input is received. The timeout period is set by a command-line argument.

**Note:** This does not stop the event from getting to whatever is running on the display. Whatever is running will still receive an event, even if the display
is off.

The program will use a linux event device like `/dev/input/event0` to receive events from the touchscreen, keyboard, mouse, etc., and `/sys/class/backlight/10-0045/brightness` to dim the backlight. The event device is a command-line parameter without the /dev/input/ path specification.


# Installation

Clone the repository and change directories:
```shell
git clone https://github.com/aklehm/backlight_dimmer.git
cd backlight_dimmer
```

Build and run it!
```shell
make
sudo chown root:root  ./timeout; sudo chmod +s ./timeout
./timeout 10 event0
```
**Caution:** I had to change the path of the brightness from `/sys/class/backlight/rpi-backlight/brightness` to `/sys/class/backlight/10-0045/brightness`. I don't know, if this the correct path on every system. If `./timeout 10 event0` returns an error, you may have to edit the path in the `timeout.c` and `run-timeout.sh` file.

Copy to `/usr/local/bin` and make it setuid to allow anyone to run it.
```shell
sudo cp ./timeout /usr/local/bin
sudo cp ./lsinput /usr/local/bin
sudo cp ./run-timeout.sh /usr/local/bin
sudo chmod +s /usr/local/bin/timeout
sudo chmod +s /usr/local/bin/run-timeout.sh
```

**Note:** It must be run as root (setuid) or with `sudo` to be able to access the backlight. Or you can make the program setuid and owned by root with `sudo cp ...` and `sudo chmod ...`

It can be run at startup, for example by putting a line in 
`/etc/rc.local`. I had to add `sudo` to get the touch input working.
```
sudo run-timeout.sh
```

Or by updating `/etc/xdg/lxsession/LXDE-pi/autostart` with 
```
@run-timeout.sh
```

**Note:** If you have the problem that the screen remains dark although the mouse, keyboard or touch display are being used, the "input-utils" package must be installed.
```shell
sudo apt install input-utils
```

&copy; Copyright 2019-2020, Dougie Lawson, all rights reserved.
&copy; Copyright 2024, Alexander Klehm, all rights reserved.
