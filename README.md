Adaptive backlight daemon, using camera as light sensor.

## Features
- smooth backlight adaptation
- start with delay
- no systemd dependency
- effective: up to 30fps for camera input without notable performance losses
- no need for X, backlight is controlled via ACPI (though xbacklight is supported too)
- auto-restart on configuration file change
- set maximum light to current by SIGUSR2 signal

## Dependencies
*Note: It will tell when necessary tools are missing.*

- **POSIX shell** (tested with [GNU Bash](http://tiswww.case.edu/php/chet/bash/bashtop.html) and [Dash](http://gondor.apana.org.au/~herbert/dash/))
- [FFMpeg](https://ffmpeg.org/)
- [coreutils](https://www.gnu.org/software/coreutils/)
- [GNU awk](https://www.gnu.org/software/gawk/gawk.html)
- [v4l-utils](https://git.linuxtv.org/v4l-utils.git)

**Optional (alternative)**

- [ncurses](https://www.gnu.org/software/ncurses/) - for colored output
- [util-linux](https://www.kernel.org/pub/linux/utils/util-linux/) - use hexdump instead of od
- [inotify-tools](https://github.com/inotify-tools/inotify-tools) - to auto-restart on configuration change

**Backlight control backends**

- [light](https://github.com/haikarainen/light)
- [xbacklight](https://gitlab.freedesktop.org/xorg/app/xbacklight) or [acpilight](sys-power/acpilight)

## Usage

Run with default settings:
```
$ backlight-adaptive
```

Toggle running - either start new or terminate existing instance:
```
$ backlight-adaptive -t
```

Update maximum light level to current:
```
$ backlight-adaptive --calibrate
```

Type `backlight-adaptive -h` for more options

**Backlight change smoothing**

Backlight can change smoothly by using multiple box filter stages. Box filters are effective at any buffer length, and 3 of them are enough to get result, comparable by quality with gaussian or cubic spline. Buffer sizes are specified independently for each filter in form of comma-separated number list (no spaces).

Examples for framerate 30fps:

Use short anti-fluctuation filter:
```
$ backlight-adaptive --smooth 6,6
```

Slow accomodation filter:
```
$ backlight-adaptive --smooth 120,120,20
```

NOTE: Two equally sized filters seem to be most optimal setup.

Lone one makes linear transition for lone jump between two stable levels, but for short fluctuation it will be just small but constant change for all duration. 2nd filter turns it to linear fade on and off. This should be enough for usable result - for stable change it makes smooth result. 3rd filter smoothes even shortest fluctuations, but each extra stage adds latency between light change and visible backlight reaction.

## Configuration

Configuration file is automatically created if no other file exists and writeable config directory is found.
Further update may be forced with --update-conf or -u command line option.
Possible places for configuration file:

- $XDG_CONFIG_DIR
- ~/.config
- /etc

Supported signals:

- SIGUSR1 - reload configuration
- SIGUSR2 - update maximum light level to current

## Under hood

Shell script only makes configuration, all dirty job is done by persistant ffmpeg-based pipeline.

## Known alternatives

- [Clight](https://github.com/FedeDP/Clight)
- [calise](https://sourceforge.net/projects/calise/)
