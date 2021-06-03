Highly reactive daemon for adaptive backlight, using camera as light sensor.

[![License: GPL v3](https://img.shields.io/badge/License-GPLv3-blue.svg)](https://www.gnu.org/licenses/gpl-3.0)

## Features

- Smooth backlight adaptation
- Light fluctuations filtering
- Auto-calibration
- Delayed start
- One-time mode
- Auto-restart on configuration file change
- Performance: up to __30fps__ for camera input without notable performance losses
- No need for __systemd__ or __X__, though xbacklight may be used

## Dependencies

#### Minimum

- __POSIX shell__ (tested with [GNU Bash](http://tiswww.case.edu/php/chet/bash/bashtop.html) and [Dash](http://gondor.apana.org.au/~herbert/dash/))
- [FFMpeg](https://ffmpeg.org/)
- [coreutils](https://www.gnu.org/software/coreutils/)
- [GNU awk](https://www.gnu.org/software/gawk/gawk.html)
- [v4l-utils](https://git.linuxtv.org/v4l-utils.git)
- [psmisc](https://gitlab.com/psmisc/psmisc)

#### Optional (alternative)

- [ncurses](https://www.gnu.org/software/ncurses/) - for colored output
- [util-linux](https://www.kernel.org/pub/linux/utils/util-linux/) - use hexdump instead of od
- [inotify-tools](https://github.com/inotify-tools/inotify-tools) - to auto-restart on configuration change

#### Backlight control backends (optional)

- [light](https://github.com/haikarainen/light)
- [xbacklight](https://gitlab.freedesktop.org/xorg/app/xbacklight) or [acpilight](sys-power/acpilight)

__Note:__ It will tell when necessary tools are missing.

## Usage

Run with default settings
```
$ backlightadapt
```

Type `backlightadapt -h` to see all options.

#### Useful options

Toggle running
```
$ backlightadapt -t
```

Match backlight once to current light level and quit.
```
$ backlightadapt -1
```

Update maximum light level to current
```
$ backlightadapt --calibrate
```

### Backlight change smoothing

Backlight can change smoothly by using chain of multiple filters. There are two supported types of filters. Median filter starts the chain, while average (box) filters go next.
First filter excludes short light fluctuations (less than half of buffer size), next filters smooth changes.

__Filters description format:__ `SIZE,SIZE...`

Filter sizes with size 0 or 1 are skiped. Setting first to such size effectively disables median filter. If it's the only element in list, smoothing is completely disabled.

#### Examples for 30fps framerate

- Instant, reflex-like reaction:
```
$ backlightadapt --smooth 0
```

- Exclude shortest fluctuations:
```
$ backlightadapt --smooth 10
```

- Smooth change:
```
$ backlightadapt --smooth 8,6,6
```

- Smoothing without fluctuation exclusion:
```
$ backlightadapt --smooth 0,6,6
```

- Slow accomodation:
```
$ backlightadapt --smooth 60,70,70
```

#### Advice

Two or three equally sized filters seem to be most optimal setup.

Lone average filter makes linear transition for lone jump between two stable levels, but for short fluctuation it will be just small but constant change for duration of filter latency.

2nd average filter turns it to linear fade on and off. This should be enough for usable result - for stable change it makes smooth result. If first filter is median - result as previous case, but no fluctuations, causing stable shifts for filter's latency.

3rd filter makes smooth wave even for shortest fluctuations (if median was disabled), but each extra stage adds notable latency in visible reaction. One median and two average filters, all equally sized, should be ideal combination in most cases.

## Configuration

Configuration file is automatically created if no other file exists and writeable config directory is found. It may be later updated from command line options by appending `--update-conf` command line option. If combined with `--pretend` option, configuration will be updated without launch.

#### Calibration

Update light / backlight ceil to current. This may be done either by running with `--calibrate` option or by sending SIGUSR2 to working instance.

#### Locations

- Environmental variable `$XDG_CONFIG_DIR`
- `~/.config`
- `/etc`

#### Supported signals

- SIGUSR1 - reload configuration
- SIGUSR2 - calibrate

#### Permissions

Following steps are necessary for internal ACPI backend to work.

- Place __90-backlight.rules__ into __/etc/udev/rules.d__ or __/lib/udev/rules.d__ to enable write permissions for internal ACPI backend (requires reboot).
- Make sure, that you are in video group (requires relogin).

__Note:__ other tools like _acpilight_ or _light_ may have it already done.

## Bug reports

Please report bugs at [github](https://github.com/nick87720z/backlight-adaptive/issues) or [opencode](https://www.opencode.net/nick87720z/backlight-adaptive/-/issues) project page.

## Under hood

Shell script only makes setup, all dirty job is done by persistant ffmpeg-based pipeline.

## Known alternatives

- [Clight](https://github.com/FedeDP/Clight)
- [calise](https://sourceforge.net/projects/calise/)
- [macbook-lighter](https://github.com/harttle/macbook-lighter)

See [more](https://wiki.archlinux.org/index.php/Backlight#Backlight_utilities) backlight utilities
