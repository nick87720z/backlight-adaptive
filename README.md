Adaptive backlight daemon, using camera as light sensor.

## Features
- backlight change smoothing
- no systemd dependency
- effective: up to 30fps for camera input without notable performance losses
- no need for X, backlight is controlled via ACPI (though xbacklight is supported too)
- auto-restart on configuration file change

## Dependencies
*Note: It will tell when necessary tools are missing.*

- posix shell (tested with [GNU Bash](http://tiswww.case.edu/php/chet/bash/bashtop.html) and [Dash](http://gondor.apana.org.au/~herbert/dash/))
- [FFMpeg](https://ffmpeg.org/)
- [coreutils](https://www.gnu.org/software/coreutils/)
- [awk](https://www.gnu.org/software/gawk/gawk.html)
- [inotify-tools](https://github.com/inotify-tools/inotify-tools)
- [v4l-utils](https://git.linuxtv.org/v4l-utils.git)

**Optional (alternative)**

- [light](https://github.com/haikarainen/light)
- [xbacklight](https://gitlab.freedesktop.org/xorg/app/xbacklight) or [acpilight](sys-power/acpilight)

## Usage

Run with default settings:
`$ backlight-adaptive`

Toggle running - either start new or terminate existing instance:
`$ backlight-adaptive -t`

Type `backlight-adaptive -h` for more options

Don't block web camera constantly:
`$ backlight-adaptive --no-grab --delay 5`

With different video input:
`$ backlight-adaptive --video-dev /dev/video1`

Disable colors:
`$ backlight-adaptive --color false`

## Configuration

Configuration file is automatically created if no other file exists and writeable config directory is found.
Possible places for configuration file:

- $XDG_CONFIG_DIR
- ~/.config
- /etc

## Under hood

Shell script, runing persistant ffmpeg-based pipeline.

## Known alternatives

- [Clight](https://github.com/FedeDP/Clight)
- [calise](https://sourceforge.net/projects/calise/)
