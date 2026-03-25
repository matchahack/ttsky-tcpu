# tcpu

> [!NOTE]
This is an `8-bit` CPU, with an integrated bootloader, and IO capability via UART

It is an accumulator style CPU, with two registers, a small amount of internal memory

## How it works

The `ISA` is described here:

```
instruction || 7:7 | 6:5  | 4:4   | 3:0      || register elaboration
======================================================================
add         || 0   | 00   | x     | x        || reg_a = reg_a + reg_b
----------------------------------------------------------------------
add 1       || 0   | 01   | x     | x        || reg_a = reg_a + 1
----------------------------------------------------------------------
and         || 0   | 10   | x     | x        || reg_a = reg_a & reg_b
----------------------------------------------------------------------
not         || 0   | 11   | x     | x        || reg_a = ~reg_a
----------------------------------------------------------------------
jmp         || 1   | 00   | x     | address  || program_counter = addr
----------------------------------------------------------------------
store       || 1   | 01   | x     | address  || data_mem[addr] = reg_a
----------------------------------------------------------------------
load        || 1   | 10   | x     | address  || reg_b = data_mem[addr]
----------------------------------------------------------------------
nop         || 1   | 11   | x     | x        || 
```

## External hardware

You will need a `USBC` to `UART` converter to program this CPU. [This is how to make your own](https://github.com/matchahack/usbc2uart.up). Or just buy a cheap one online.

> [!IMPORTANT]
Plug in the `USBC2UART` converter, and use `ls /devttyUSB*` to find out which interface to use for programming.

## How to test

Plug in the `USBC2UART` and connect the `TX`/`RX`/`GND` wires correctly, then program the CPU with a list of instructions:
```
# Repeated ADD 1
python programmer.py -p /dev/ttyUSB2 -b 115200 -i "[0x20, 0x20, 0x20, 0x20, 0x20, 0x20, 0x20, 0x20]"
# LOAD, ADD 1, STORE, LOAD, repeating NOP
python programmer.py -p /dev/ttyUSB2 -b 115200 -i "[0xC0, 0x20, 0xA0, 0xC0, 0xFF, 0xFF, 0xFF, 0xFF]"
# ADD 1, JUMP to 5, repeating ADD 1
python programmer.py -p /dev/ttyUSB2 -b 115200 -i "[0x20, 0x85, 0x20, 0x20, 0x20, 0x20, 0x20, 0x20]"
```
