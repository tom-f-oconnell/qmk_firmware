
### Expected hardware

- Keyboard: [Mysterium v2](https://github.com/coseyfannitutti/mysterium)

- Computer for compilation / upload / testing of the keyboard: Ubuntu 18.04 / 20.04

- USBasp programmer, which Han had updated the firmware of to
  `usbasp.atmega8.2011-05-28.hex`, as per his guide.


### Setting up this fork of QMK

Tested initially on Ubuntu 18.04 and most recently on 20.04.

```
cd ~/src
git clone https://github.com/tom-f-oconnell/qmk_firmware
cd qmk_firmware
git remote add upstream https://github.com/qmk/qmk_firmware

# (assuming you have python3.8 installed; get from deadsnakes PPA w/ venv if not)
python3.8 -m venv venv
source venv/bin/activate
pip install --upgrade pip
pip install qmk

# This should also setup any Ubuntu dependencies, such as avrdude et al.
qmk setup -H ~/src/qmk_firwmare -y

# If you do this, you can omit the -kb/-km arguments from QMK commands using them.
qmk config user.keyboard=coseyfannitutti/mysterium user.keymap=tom
```

#### Keeping the fork up-to-date

TODO fill this out next time upstream has changes and I update, but I think it was
roughly:
```
git fetch origin
git rebase origin/master

# (carefully, and after finishing the rebase process)
git push --force
```


### Flashing bootloader on the ATmega32A

Connect the USBasp ISP connector to the corresponding connector on the keyboard. Orient
the connectors such that the ground pins line up. There is no apparent need to connect
the USB-C cable at this point, though it also didn't seem to hurt.

USBasp jumper configuration:
- 2-pin jumper: open
- 3-pin 3.3/5v selector: 5v (ATmega32A seems like it might also work with 3.3v)

```
git clone https://github.com/coseyfannitutti/USBaspLoader
cd USBaspLoader
git checkout atmega32a
```

To use my USBasp programmer, I replaced line 41 in `Makefile.inc` with the (previously
commented):
```
PROGRAMMER = -c usbasp
```

Finally:
```
make flash
make fuse
```

The above was taken from [these instructions](https://github.com/coseyfannitutti/mysterium/tree/master/doc/bootloader)
in the Mysterium github.


### Uploading the QMK firmware

#### Entering bootloader mode

On the first upload after setting up the bootloader: press and hold `BOOT`, tap `RESET`,
and release `BOOT` before / during the `qmk flash` command. On subsequent uploads, you
may also press `Fn+r`.

#### Flashing

```
cd ~/src/qmk_firmware

# Activate the virtual environment created in a previous step
source venv/bin/activate

qmk flash
```

In both cases, you may need to press `RESET` after upload (or power cycle the keyboard),
to be able to use the keyboard again.


### Testing the keyboard

NOTE: need to update this section to what would be seen after flashing bootloader, as
well as after flashing firmware using the bootloader.

Disconnect the ISP connector and connect the keyboard to your 18.04 computer via USB-C.

You should see some lines appear at the end of `dmesg` output that look like:
```
[ 7812.525212] usb 1-6: new low-speed USB device number 98 using xhci_hcd
[ 7812.756487] usb 1-6: New USB device found, idVendor=6b62, idProduct=8769, bcdDevice= 0.01
[ 7812.756493] usb 1-6: New USB device strings: Mfr=1, Product=2, SerialNumber=0
[ 7812.756496] usb 1-6: Product: MYSTERIUM
[ 7812.756499] usb 1-6: Manufacturer: coseyfannitutti
[ 7812.792599] input: coseyfannitutti MYSTERIUM as /devices/pci0000:00/0000:00:14.0/usb1/1-6/1-6:1.0/0003:6B62:8769.0016/input/input52
[ 7812.853931] hid-generic 0003:6B62:8769.0016: input,hidraw6: USB HID v1.01 Keyboard [coseyfannitutti MYSTERIUM] on usb-0000:00:14.0-6/input0
[ 7812.870612] input: coseyfannitutti MYSTERIUM System Control as /devices/pci0000:00/0000:00:14.0/usb1/1-6/1-6:1.1/0003:6B62:8769.0017/input/input53
[ 7812.929540] input: coseyfannitutti MYSTERIUM Consumer Control as /devices/pci0000:00/0000:00:14.0/usb1/1-6/1-6:1.1/0003:6B62:8769.0017/input/input54
[ 7812.929949] hid-generic 0003:6B62:8769.0017: input,hidraw7: USB HID v1.01 Device [coseyfannitutti MYSTERIUM] on usb-0000:00:14.0-6/input1
```

Use the online QMK keyboard tester [here](https://config.qmk.fm/#/test). With my first
test of the mysterium, using a single unsoldered keyboard switch to test everything, I
got this output:
![keyboard tester screenshot](docs/tom_mysterium_1st_test.png)


### More information

Original `readme.md` contents below:

# Quantum Mechanical Keyboard Firmware 

[![Current Version](https://img.shields.io/github/tag/qmk/qmk_firmware.svg)](https://github.com/qmk/qmk_firmware/tags)
[![Discord](https://img.shields.io/discord/440868230475677696.svg)](https://discord.gg/Uq7gcHh)
[![Docs Status](https://img.shields.io/badge/docs-ready-orange.svg)](https://docs.qmk.fm)
[![GitHub contributors](https://img.shields.io/github/contributors/qmk/qmk_firmware.svg)](https://github.com/qmk/qmk_firmware/pulse/monthly)
[![GitHub forks](https://img.shields.io/github/forks/qmk/qmk_firmware.svg?style=social&label=Fork)](https://github.com/qmk/qmk_firmware/)

This is a keyboard firmware based on the [tmk\_keyboard firmware](https://github.com/tmk/tmk_keyboard) with some useful features for Atmel AVR and ARM controllers, and more specifically, the [OLKB product line](https://olkb.com), the [ErgoDox EZ](https://ergodox-ez.com) keyboard, and the [Clueboard product line](https://clueboard.co).

## Documentation

* [See the official documentation on docs.qmk.fm](https://docs.qmk.fm)

The docs are powered by [Docsify](https://docsify.js.org/) and hosted on [GitHub](/docs/). They are also viewable offline; see [Previewing the Documentation](https://docs.qmk.fm/#/contributing?id=previewing-the-documentation) for more details.

You can request changes by making a fork and opening a [pull request](https://github.com/qmk/qmk_firmware/pulls), or by clicking the "Edit this page" link at the bottom of any page.

## Supported Keyboards

* [Planck](/keyboards/planck/)
* [Preonic](/keyboards/preonic/)
* [ErgoDox EZ](/keyboards/ergodox_ez/)
* [Clueboard](/keyboards/clueboard/)
* [Cluepad](/keyboards/clueboard/17/)
* [Atreus](/keyboards/atreus/)

The project also includes community support for [lots of other keyboards](/keyboards/).

## Maintainers

QMK is developed and maintained by Jack Humbert of OLKB with contributions from the community, and of course, [Hasu](https://github.com/tmk). The OLKB product firmwares are maintained by [Jack Humbert](https://github.com/jackhumbert), the Ergodox EZ by [ZSA Technology Labs](https://github.com/zsa), the Clueboard by [Zach White](https://github.com/skullydazed), and the Atreus by [Phil Hagelberg](https://github.com/technomancy).

## Official Website

[qmk.fm](https://qmk.fm) is the official website of QMK, where you can find links to this page, the documentation, and the keyboards supported by QMK.
