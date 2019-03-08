# Debianbook i3 (how to)
Debian (netinst) + i3wm on macbook pro retina 2013 (11.1)

I got debian + i3wm working very stable on my macbook pro retina 2013, this is my annotations how to solve some problems and versioned my config files.

## Installing Debian (netinst)

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

## screen resolution (dpi)

- put this on `/etc/X11/xorg.conf.d/10-monitor.conf`
```
Section "Monitor"
    Identifier             "<default monitor>"
    DisplaySize            400 250 # In millimeters
EndSection
```

## keyboard schema

I want to the keyboard be as close as my experience on macOs, cuz i still use another macbook. This will make these changes:

- `caps look` as `ctrl`
- `cmd` as `ctrl` (also `i3 mod` key)
- `altGr` on `left alt` (to make the mac like accentuation. eg: `alt+e` then `a` = `á`)

How to:

- add `00-keyboard.conf` to `/etc/X11/xorg.conf.d`
```
Section "InputClass"
	Identifier "system-keyboard"
	MatchIsKeyboard "on"
	Option "XkbLayout" "us"
	Option "XkbModel" "macbook78"
	Option "XkbVariant" "mac"
	Option "XkbOptions" "lv3:alt_switch,ctrl:nocaps,altwin:ctrl_win"
EndSection
```
*but wait... there is no mac variant to `us` symbols... so I make it*
- append this on `/usr/share/X11/xkb/symbols/macintosh_vndr/us`:
```
partial alphanumeric_keys
xkb_symbols "mac" {

    include "macintosh_vndr/us(extended)"
    name[Group1]= "English (Macintosh)";
    key.type[group1]="FOUR_LEVEL";

    key <LSGT> { [   section,  plusminus,       section,        plusminus ] };
    key <TLDE> { [     grave, asciitilde,    dead_grave,        dead_horn ] };
    key <AE01> { [         1,     exclam,    exclamdown,            U2044 ] };
    key <AE02> { [         2,         at,     trademark,         EuroSign ] };
    key <AE03> { [         3, numbersign,      sterling,            U2039 ] };
    key <AE04> { [         4,     dollar,          cent,            U203A ] };
    key <AE05> { [         5,    percent,      infinity,            UFB01 ] };
    key <AE06> { [         6,asciicircum,       section,            UFB02 ] };
    key <AE07> { [         7,  ampersand,     paragraph,     doubledagger ] };
    key <AE08> { [         8,   asterisk, enfilledcircbullet,      degree ] };
    key <AE09> { [         9,  parenleft,   ordfeminine,   periodcentered ] };
    key <AE10> { [         0, parenright,     masculine,singlelowquotemark] };
    key <AE11> { [     minus, underscore,        endash,           emdash ] };
    key <AE12> { [     equal,       plus,      notequal,        plusminus ] };

    key <AD01> { [         q,          Q,            oe,               OE ] };
    key <AD02> { [         w,          W,         U2211,doublelowquotemark] };
    key <AD03> { [         e,          E,    dead_acute,            acute ] };
    key <AD04> { [         r,          R,    registered,            U2030 ] };
    key <AD05> { [         t,          T,        dagger,       dead_caron ] };
    key <AD06> { [         y,          Y,           yen,       onequarter ] };
    key <AD07> { [         u,        U,  dead_diaeresis,        diaeresis ] };
    key <AD08> { [         i,        I, dead_circumflex,            U02C6 ] };
    key <AD09> { [         o,          O,        oslash,         Ooblique ] };
    key <AD10> { [         p,          P,      Greek_pi,            U220F ] };
    key <AD11> { [ bracketleft,  braceleft, leftdoublequotemark, rightdoublequotemark ] };
    key <AD12> { [bracketright, braceright, leftsinglequotemark, rightsinglequotemark ] };
    key <BKSL> { [ backslash,        bar, guillemotleft,   guillemotright ] };

    key <AC01> { [         a,          A,         aring,            Aring ] };
    key <AC02> { [         s,          S,        ssharp,      dead_stroke ] };
    key <AC03> { [         d,          D, partialderivative,          eth ] };
    key <AC04> { [         f,          F,      function,        dead_hook ] };
    key <AC05> { [         g,          G,     copyright, dead_doubleacute ] };
    key <AC06> { [         h,          H, dead_abovedot,    dead_belowdot ] };
    key <AC07> { [         j,          J,         U2206,          onehalf ] };
    key <AC08> { [         k,          K,dead_abovering,            UF8FF ] };

    key <AC09> { [         l,          L,       notsign,            THORN ] };
    key <AC10> { [ semicolon,      colon,         U2026,            thorn ] };
    key <AC11> { [apostrophe,   quotedbl,            ae,               AE ] };

    key <AB01> { [         z,          Z,   Greek_OMEGA,     dead_cedilla ] };
    key <AB02> { [         x,          X,         U2248,      dead_ogonek ] };
                                // unclear whether "approxeq" is 2248 or 2245
    key <AB03> { [         c,          C,      ccedilla,         Ccedilla ] };
    key <AB04> { [         v,          V,    squareroot,            U25CA ] };
    key <AB05> { [         b,          B,      integral,         idotless ] };
    key <AB06> { [         n,          N,    dead_tilde,            U02DC ] };
    key <AB07> { [         m,          M,            mu,    threequarters ] };
    key <AB08> { [     comma,       less, lessthanequal,      dead_macron ] };
    key <AB09> { [    period,    greater, greaterthanequal,    dead_breve ] };
    key <AB10> { [     slash,   question,      division,     questiondown ] };

    include "level3(ralt_switch)"
};
```

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


