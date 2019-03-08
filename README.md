I got debian + i3wm working very stable on my macbook pro retina 2013, this is my annotations how to solve some problems and versioned my config files.

# Installing Debian (netinst) and i3wm on macbook pro 11.2

Install `debian netinst`, then you can use an android tethering internet connection:
```
    dmsg #to get port mapping when connecting the android device
    dhclient ubsmappeddevice #to start internet
```

## Wifi
then solved via https://wiki.debian.org/wl 

- add non-free source list
- install `linux-image-amd64`, `linux-headers-amd64` and `broadcom-sta-dkms`
- disable conflicting modules: `modprobe -r b44 b43 b43legacy ssb brcmsmac bcma`
- initialize wl: `modprobe wl`
- install an wifi management like `wicd` (it has an applet to i3)

With wifi working you can install **i3wm** and start customizing it:

## keyboard schema

// todo

## Default to Fn keys
(rather than media keys)

```
    # temporary
    echo 2 > /sys/module/hid_apple/parameters/fnmode

    # permanent
    echo options hid_apple fnmode=2 | sudo tee -a /etc/modprobe.d/hid_apple.conf
```
## Screen Brightness Control

- install `xbacklight`
- fix address of device in `sudo find /sys/ -type f -iname '*brightness*'` (search for anything like intel/brightness)
- set it on xorg.conf:
```
      Section "Device"
        Identifier  "Card0"
        Driver      "intel"
        Option      "Backlight"  "/sys/devices/pci0000:00/0000:00:02.0/drm/card0/card0-eDP-1/intel_backlight"
      EndSection
```
- i3 config shortcuts
```
    # brightness
    bindsym XF86MonBrightnessDown exec xbacklight -dec 20 # decrease 
    screen brightness
    bindsym XF86MonBrightnessUp exec xbacklight -inc 20 # increase screen 
    brightness
```


## Sound / Player Control

- install `pulseaudio` and `alsa-utils`; reboot;
- install `playerctl` from github (https://github.com/acrisci/playerctl)
- i3 config shortcuts
```
    # audio
    bindsym XF86AudioRaiseVolumei exec --no-startup-id pactl set-sink-volume 0 +5% #increase sound volume
    bindsym XF86AudioLowerVolume exec --no-startup-id pactl set-sink-volume 0 -5% #decrease sound volume
    bindsym XF86AudioMute exec --no-startup-id pactl set-sink-mute 0 toggle # mute sound

    # player
    bindsym XF86AudioPlay exec playerctl play-pause
    bindsym XF86AudioNext exec playerctl next
    bindsym XF86AudioPrev exec playerctl previous
```

## Keyboard Backlight Control

- install light from github (https://github.com/haikarainen/light)
- Fix for version 1.2, after install:
```
    sudo cp /home/alexandre/90-backlight.rules.bkp /lib/udev/rules.d/
```
- i3 config shortcuts
```
# keyboard backlight
bindsym XF86KbdBrightnessDown exec light -U 33 -s sysfs/leds/smc::kbd_backlight
bindsym XF86KbdBrightnessUp exec light -A 33 -s sysfs/leds/smc::kbd_backlight
```

## invert trackpad scrolling

- install `xinput`
- add this to `.xsessionrc`:
```
    xinput --set-prop bcm5974 "libinput Natural Scrolling Enabled" 1
    xinput --set-prop bcm5974 "libinput Tapping Enabled" 1
```

## screenshot

- install `scrot`
- bind i3 shortcut
```
bindsym --release $mod+Shift+3 exec "scrot -s /tmp/screenshot-$(date +%F_%T).png -e 'xclip -selection c -t image/png < $f'"
```

## alt + tab

- you can bind alt+tab to change workspace
```
bindsym $mod+Tab workspace next
```

## status bar

- copy `.i3status.conf` to your home directory.


