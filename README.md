# can_usb_adapter

USB-C to dual CAN-FD adapter. On Linux it shows up as `can0` and `can1`, no drivers needed.

<!-- ![can_usb_adapter](docs/board.jpg) -->

## Board

- STM32G0B1CBT6, 8 MHz crystal
- 2× TJA1051T/3 CAN transceivers with ESD protection
- USB-C (USB powered), AP2112K 3.3 V regulator
- 120 Ω termination on both channels
- 2-layer PCB, 45 × 25 mm. Gerbers, BOM and CPL are in [can_usb_adapter/](can_usb_adapter/)

| Connector | Function | Pins |
|---|---|---|
| J1 | USB-C | |
| J3 | CAN 1 (`can0`), JST GH | 1 GND, 2 CANH, 3 CANL |
| J4 | CAN 2 (`can1`), JST GH | 1 GND, 2 CANH, 3 CANL |
| J2 | SWD | 1 3V3, 2 SWCLK, 3 GND, 4 SWDIO, 5 NRST |

The pinout follows candleLight FD, so the firmware runs unmodified.

## Firmware

The firmware is [CANnectivity](https://github.com/CANnectivity/cannectivity) by Henrik Brix Andersen and
contributors, licensed under Apache 2.0 (see [firmware/LICENSE](firmware/LICENSE)). The binary in
[firmware/](firmware/) is built from unmodified sources (v1.5.0-10-g53869cc, board `candlelightfd/stm32g0b1xx/dual`).

## Flashing

```bash
dfu-util -a 0 -s 0x08000000:leave -D firmware/cannectivity-v1.5.0-10-g53869cc-candlelightfd-dual.bin
```

Then replug USB.

## Usage

```bash
sudo apt install can-utils

# CAN, 1 Mbit/s
sudo ip link set can0 up type can bitrate 1000000

# CAN-FD, 1 / 5 Mbit/s
sudo ip link set can0 up type can bitrate 1000000 dbitrate 5000000 fd on

candump can0                  # listen
cansend can0 123#DEADBEEF     # send CAN frame
cansend can0 123##1DEADBEEF   # send CAN-FD frame

sudo ip link set can0 down    # before changing settings
```
