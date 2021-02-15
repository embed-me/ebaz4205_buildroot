# meta-ebaz4205

The EBAZ4205 was originally developed as cryptomining control board.
Due to it's low price on the marked it is also perfect to learn
the Zynq platform. This br2-external tree provides everything to build
a linux system with basic BSP (Board Support Package).

## Maintainer

	Lukas Lichtl (admin@embed-me.com)

## Dependencies

This layer depends on:

	URI: git://git.busybox.net/buildroot
	branch: 2020.11.x

## Build

	make BR2_EXTERNAL=/path/to/ebaz4205_buildroot zynq_ebaz4205_defconfig
	make 2>&1 | tee build.log

## Installation

The following files from within the output/images/ need to be copied
to a SD-Card with a FAT-formatted partition with boot flag set:

  * boot.bin
  * ebaz4205_top.bin (from the Vivado build)
  * ebaz4205-zynq7.dtb
  * rootfs.cpio.uboot
  * u-boot.bin
  * u-boot.img
  * uEnv.txt
  * uImage

## BSP Details

### FSBL(First Stage Bootloader)

Basic system initialization.

### U-Boot(Second Stage Bootloader)

Tailored for SD-Boot, since this is how the board is mainly used.
In this distribution, u-boot (instead of the FSBL) is responsible
for configuring the Bitstream. Wait time during boot is set to zero.
In order to interrupt the boot process, press and hold Button S2
during boot and you will end up in the u-boot console.

In order to identify boot issues, the following LED codes are.

| LED red | LED green | Boot State | Description           |
|:--------|:----------|-----------:|:----------------------|
| on      | on        |          0 | init/pwr up           |
| on      | off       |          1 | Bitstream config done |
| off     | on        |          2 | DDR load done         |
| off     | off       |          3 | -                     |

### Linux

#### Credentials

The systems default credentials are `root` / `root`.

#### Init

As init system, busybox was chosen.

#### Ethernet

The phy on the board supports 10/100Mbit and the image
makes use of Dropbears SSH-server. This allows you to
connect to the board without the use of the serial console.

#### LEDs

In order to identify that linux was booted as intended and
the system is running, the green LED provides a heartbeat.

| LED red | LED green | Boot State | Description |
|:--------|:----------|-----------:|:------------|
| off     | heartbeat |          0 | init done   |

#### Mount Points

The boot partition is mounted on `/media/mmcplk0p1`.
This allows access to the boot files while the system
is running (eg. to modify uEnv.txt or provide updates).

#### Buttons

Button presses can be detected in user-space
using `/dev/input/event0`.

#### LEDs

Both LEDs on the front (green, red) are accessable
from within user space `/sys/class/leds`.