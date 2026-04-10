# PCB for driving an LED matrix

This PCB is meant to write and read from PMOD pins and drive an LED matrix based on these pins.
There will be a microcontroller that drives the LED matrix so it can also have an independent program to display arbitrary things on the matrix.

The PCB should be able to be powered externally as well as through a battery.
The reason to want to use a battery is that it can then be used as a conference badge to display a name on.

The PCB should connect to the Tiny Tapeout demo board.

![pinout](https://github.com/TinyTapeout/tt-demo-pcb/blob/main/doc/img/tt-etr-dbv3p2-pinout.png)

## Component Specs

- RP Pico H
    - <https://www.raspberrypi.com/documentation/microcontrollers/raspberry-pi-pico.html>
    - 26 GPIO pins
    - Supply power to VSYS via a Schottky diode
        - ~2.3 V - 5.5 V
- LEDs (KINGBRIGHT TA07-11SEKWA)
    - <https://uk.farnell.com/kingbright/ta07-11sekwa/dot-matrix-0-7-cmn-anode-orange/dp/2080062>
    - Column anode (row cathode)
    - I_F = 10 mA, I_Fmax = 30 mA
    - V_F = 2 V_typ (@ I_F = 10mA)
    - R_7ma = (3.3 V - 2 V) / 7 mA = ~200 Ohm (186 Ohm)

## LED Multiplexing

Row-wise (one row at a time):

- 5 resistors (one per column)
- 5 LEDs max on one pin
    - 10 mA max per LED without transistor
- 7 phase update

Column-wise (one column at a time):

- 7 resistors (one per row)
- 7 LEDs max on one pin
    - 7 mA max per LED without transistor
- 5 phase update

Try using PIO to do the high-frequency stuff?
[PIO emulator](https://github.com/soundpaint/rp2040pio).

## Pins

12 GPIO for the LEDs.

7 GPIO for PMOD output to Tiny Tapeout.

5 GPIO pins for PMOD input from Tiny Tapeout.
Connect some to UART pins.

2 GPIO (or 1 ADC pin) for DIP switches.

Connect RUN pin to a normally-open momentary switch to GND to get a Reset button.

## PCB schematic and design

We'll probably use a cheap PCB manufacturer like: [JLC](https://jlcpcb.com/).
Previously I had also considerd [PCB way](https://www.pcbway.com/).

## Farnell

1x [Raspberry Pi Pico H](https://uk.farnell.com/raspberry-pi/raspberry-pi-pico-h/raspberry-pi-board-arm-cortex/dp/3996081)
1x [LED Matrix](https://uk.farnell.com/kingbright/ta07-11sekwa/dot-matrix-0-7-cmn-anode-orange/dp/2080062)
1x [Reset Switch](https://uk.farnell.com/te-connectivity-alcoswitch/1825910-3/tactile-switch-spst-0-05a-24v/dp/3406898)
2x [Schottky Diode](https://uk.farnell.com/stmicroelectronics/bat48/diode-schottky-small-signal/dp/9801472)
1x [DIP Switch](https://uk.farnell.com/te-connectivity/ade0204/switch-dil-2way/dp/1123936)
1x [Battery Holder](https://uk.farnell.com/keystone/3002/battery-smd-retainer-20mm/dp/1650693)
2x [PMOD Header](https://uk.farnell.com/amphenol-fci/10129382-912003blf/btb-conn-header-12pos-2row-2-54mm/dp/3497357)
2x [RPi Pico Socket](https://uk.farnell.com/samtec/ces-120-01-t-s/receptacle-2-54mm-single-20way/dp/1667525)
5x? [LED Resistors](https://uk.farnell.com/multicomp/mf25-200r/res-200r-1-250mw-axial-metal-film/dp/9341471) although may already have some.

## Shift registers

To make sure we can drive all the PMOD input and output, we were thinking it's a good idea to use a shift register to save GPIO pins on the Pico. One thing Elliot mentioned was it would be good to have one full PMOD directly connected to the GPIO so that UART still works.

8-bit parallel input to serial output: [TI SN74HC165N](https://uk.farnell.com/texas-instruments/sn74hc165n/ic-8bit-parallel-load-shift-regist/dp/3120856), 8 inputs with 1 ouput and 2 control signals.

Serial input to 8-bit parallel output: [TI SN74HC595N](https://uk.farnell.com/texas-instruments/sn74hc595n/shift-register-8bit-85deg-c-pdip/dp/3120865), 8 outputs with 1 input and 2 control signals.

Harry's tip: use 74-series logic to get good datasheets.

