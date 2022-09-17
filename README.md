# PocketChip M8
A build of m8c specifically for the PocketChip. This allows you to run a M8 in headless mode using a Teensy 4.1 device over USB.
The build scripts have been modified to work on the PocketChip and the controls have been remapped to be more friendly on the PocketChip's keyboard.

## New Controls
The default key mappings have been updated to make it nicer to use on the PocketChip.

The following are configurable. These are the defaults.
* Arrow keys - as expected.
* Shift - 1 or Minus
* Option - 2 or 0
* Edit - E or 9
* Start/Stop - S or Space
* Reset - R

The following are not configurable.
* Enter "Jazz" keyboard mode - ESC
* Enter/Exit fullscreen - ALT + Enter
* Quit m8c - ALT + Q

## Prerequisites
TODO: detailed instruction.

* Upgrade the PocketChip to Debian 10 (buster)
  * You will need to remove the mali drivers so mesa can be used instead. This isn't ideal.
  * [This guide](https://nytpu.com/gemlog/2021-04-15.gmi) worked well for me. Pay close attention to the instructions.
* Build or copy the m8c binary. The m8c instructions will work as long as you use the Makefile from this project.
  * I recommend just using the release to save disk space. You may need to install/update libsdl2 in that case.
* You'll need pulseaudio installed so you can create the loopback device.
* You'll need `pavucontrol` so you can control the loopback device. Probably not necessary, but I find working with `pactl` painful.
  * This _barely_ works on the PocketChip's screen resolutionl. (pavucontrol devs, please make your thing scalable.)
  * You can get it to scroll if you click on the slider thumb and drag vertically so you can see items down the page.
  * The tab key is also required for using the dropdowns.

## Running m8c
* Connect the Teensy to USB.
* Create the loopback device.
  * `pactl add-module module-loopback`
* Run m8c.
  * m8c is compiled to just exit if the device isn't found. If you are trying to launch m8c and it doesn't seem to work, it's probably because the device can't be found.
  * I use JWM and I created a launcher for m8c so I can start it without a terminal which is nicer for the PocketChip where multiple windows don't work well.
  * The next step is easier if you have something playing on the M8, but isn't necessary.
* Use `pavucontrol` to configure the loopback device.
  * Go to the "Recording" tab and select the M8 Audio as the capture device.
  * Go to the "Playback" tab and ensure the stream is being output to the Chip's audio device.
    * You may need to fight with scrolling the UI here, but it'll be there.
* You should be able to hear the M8 now.

## Troubleshooting
* Pulse Audio gets angry a lot. `killall pulseaudio` or `pulseaudio -k` is your friend to get back into a good state.
* By default PA will suspend idle devices. This is really annoying in this case. edit /etc/pulse/default.pa and comment out the line where the `load-module module-suspend-on-idle` line.
* By default, the sound devices are all muted? Use `alsamixer` then F6 to select the Chip's audio device. Unmute the Power Amp devices.

----------------------------------------------------------------------------

# m8c (Upstream)

m8c is a client for Dirtywave M8 tracker's headless mode. The application should be cross-platform ready and can be built in Linux, Windows (with MSYS2/MINGW64) and Mac OS.

Please note that routing the headless M8 USB audio isn't in the scope of this program -- if this is needed, it can be achieved with tools like Pipewire, Pulseaudio, Jack w/ alsa\_in and alsa\_out just to name a few. The file AUDIOGUIDE.md contains some examples for routing the audio.

If you want to route audio with the headless client you could try https://github.com/booss/rm8 which is a great native client with audio support (among other user features)!

Many thanks to:

* Trash80 for the great M8 hardware and the original font (stealth57.ttf) that was converted to a bitmap for use in the progam.
* driedfruit for a wonderful little routine to blit inline bitmap fonts, https://github.com/driedfruit/SDL_inprint/
* marcinbor85 for the slip handling routine, https://github.com/marcinbor85/slip
* turbolent for the great Golang-based g0m8 application, which I used as reference on how the M8 serial protocol works.
* *Everyone who's contributed to m8c!*

Disclaimer: I'm not a coder and hardly understand C, use at your own risk :)

-------

# Installation

## Windows / MacOS

There are prebuilt binaries available in the [releases section](https://github.com/laamaa/m8c/releases/) for Windows and recent versions of MacOS.

## Linux / MacOS (building from source)

These instructions are tested with Raspberry Pi 3 B+ and Raspberry Pi OS with desktop (March 4 2021 release), but should apply for other Debian/Ubuntu flavors as well. The begining on the build process on OSX is slightly different at the start, and then the same once packages are installed.

The instructions assume that you already have a working Linux desktop installation with an internet connection.

Open Terminal and run the following commands:

### Install required packages (Raspberry Pi, Linux)

```
sudo apt update && sudo apt install -y git gcc make libsdl2-dev libserialport-dev
```
### Install required packages (OSX)

This assumes you have [installed brew](https://docs.brew.sh/Installation)

```
brew update && brew install git gcc make sdl2 libserialport pkg-config
```
### Download source code (All)

```
mkdir code && cd code
git clone https://github.com/laamaa/m8c.git
 ```

### Build the program

```
cd m8c
make
 ```

### Start the program

Connect the M8 or Teensy (with headless firmware) to your computer and start the program. It should automatically detect your device.

```
./m8c
```

If the stars are aligned correctly, you should see the M8 screen.

-----------

## Keyboard mappings

Keys for controlling the progam:

* Up arrow = up
* Down arrow = down
* Left arrow = left
* Right arrow = right
* a / left shift = select
* s / space = start
* z / left alt = opt
* x / left ctrl = edit

Additional controls:
* Alt + enter = toggle full screen / windowed
* Alt + F4 = quit program
* Delete = opt+edit (deletes a row)
* Esc = toggle keyjazz on/off 
* r / select+start+opt+edit = reset display (if glitches appear on the screen, use this)

### Keyjazz
Keyjazz allows to enter notes with keyboard, oldschool tracker-style. The layout is two octaves, starting from keys Z and Q.
When keyjazz is active, regular a/s/z/x keys are disabled. The base octave can be adjusted with numpad star/divide keys and the velocity can be set 

* Numpad asterisk (\*): increase base octave
* Numpad divide (/): decrease base ooctave
* Numpad plus (+): increase velocity
* Numpad minus (-): decrease velocity

## Gamepads

The program uses SDL's game controller system, which should make it work automagically with most gamepads. On startup, the program tries to load a SDL game controller database named gamecontrollerdb.txt from the same directory as the config file. If your joypad doesn't work out of the box, you might need to create custom bindings to this file, for example with [SDL2 Gamepad Tool](https://generalarcade.com/gamepadtool/).

## Config

Keyboard and game controller bindings can be configured via `config.ini`.

If not found, the file will be created in one of these locations:
* Windows: `C:\Users\<username>\AppData\Roaming\m8c\config.ini`
* Linux: `/home/<username>/.local/share/m8c/config.ini`
* MacOS: `/Users/<username>/Library/Application Support/m8c/config.ini`

See the `config.ini.sample` file to see the available options.

Enjoy making some nice music!

-----------

## FAQ

* When starting the program, something like the following appears and the program does not start:
```
$ ./m8c
INFO: Looking for USB serial devices.
INFO: Found M8 in /dev/ttyACM1.
INFO: Opening port.
ERROR: Error: Failed: Permission denied
```

This is likely caused because the user running m8c does not have permission to use the serial port. The eaiest way to fix this is to add the current user to a group with permission to use the serial port.

On Linux systems, look at the permissions on the serial port shown on the line that says "Found M8 in":
```
$ ls -la /dev/ttyACM1
crw-rw---- 1 root dialout 166, 0 Jan  8 14:51 /dev/ttyACM0
```

This shows that the serial port is owned by the user 'root' and the group 'dialout'. Both the user and the group have read/write permissions. To add a user to the group, run this command, replacing 'dialout' with the group shown on your own system:

    sudo adduser $USER dialout

You may need to log out and back in or even fully reboot the system for this change to take effect, but this will hopefully fix the problem. Please see [this issue for more details](https://github.com/laamaa/m8c/issues/20).

-----------

### Bonus content: quickly install m8c locally with nix

``` sh
nix-env -iA m8c-stable -f https://github.com/laamaa/m8c/archive/refs/heads/main.tar.gz
```
