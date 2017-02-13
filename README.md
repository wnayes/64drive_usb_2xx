64drive USB loader for Linux
============================
(c) 2014-2016 Retroactive

Ported to Linux based on [previous port by jkbenaim](https://github.com/jkbenaim/64drive_usb_2xx).

This is based on the `Oct 08 2016` revision of the `64drive_usb_2xx.zip`.

Requires [64drive firmware 2.X](http://64drive.retroactive.be/support.php) and [FTDI's D2XX driver package](http://www.ftdichip.com/Drivers/D2XX.htm).

## Usage
```
   -l <file> [bank] [addr]        Load binary to bank
   -d <file> <bank> <addr> <len>  Dump binary from bank
               1 - Cartridge ROM              2 - SRAM 256kbit
               3 - SRAM 768kbit               4 - FlashRAM 1Mbit
               5 - FlashRAM 1Mbit (PokeStdm2) 6 - EEPROM 16kbit

   -s <int>                       Set save emulation type
               0 - None (default)       1 - EEPROM 4k
               2 - EEPROM 16k           3 - SRAM 256kbit
               4 - FlashRAM 1Mbit       5 - SRAM 768kbit (Dezaemon 3D)
               6 - FlashRAM 1Mbit (PokeStdm2)
   -c <int>                       Set CIC emulation type (RevB/HW2 only)
               0 - 6101 (NTSC)          1 - 6102 (NTSC)
               2 - 7101 (PAL)           3 - 7102 (PAL)
               4 - x103 (All)           5 - x105 (All)
               6 - x106 (All)           7 - 5101 (NTSC)
   -b <file.rpk>                  Update bootloader
   -f <file.rpk>                  Update firmware
   -v                             Verbose output for debug

1. Arguments: <required>, [optional]
2. Address and size values are hexadecimal and byte-addressed.
3. Any bank can be loaded at any time. However, some banks overlap
   each other and so only one save bank should be used at once.
4. Save banks can be loaded/read regardless of what the 64drive has 
   been instructed to emulate.
5. Refer to the 64drive Hardware Specification for more information.
6. Use the verbose flag when updating firmware or bootloader to see
   detailed revision information about that update.
7. About CICs:
   Nintendo confusingly named the first few CICs before settling on 
   a standardization for the later ones.
     6101 - NTSC, only used in Starfox
     6102 - NTSC, used in virtually all games
     7101 - PAL,  same as 6102 but different region
     7102 - PAL,  only used in Lylat Wars
     x103 - All,  some first-party games
     x105 - All,  Zelda, many Rareware games
     x106 - All,  handful of first-party games
   The bootcode used by the game image (0x40-0xFFF) is "signed" by
   a secret number cooked into its associated CIC.
   So, you will need to specify the correct CIC to match the 
   bootcode - which itself is not region-affiliated.
   You can also use the "friendly" name, i.e. "6102" instead of the
   number index as the parameter.
```

## Building

Similarly to the Windows version, you will need some files from
[FTDI's D2XX driver package](http://www.ftdichip.com/Drivers/D2XX.htm):
  - ftd2xx.h
  - libftd2xx.a
  - WinTypes.h

Copy these files into the project, then run ```make ```.

## Usage

I have only tested the cartridge dumping features with the UltraSave.

To use it with the UltraSave, you would run something like the following:

```./64drive_usb -x -v -d OutputFile.z64 1 10000000 800000```

- `1` specifies the Cartridge ROM, as stated in the Usage.
- `10000000` indicates that the read should begin at the start of the Cartridge Space.
- The third number is the amount of bytes to read, so this should be changed based on the ROM. If you aren't sure, the largest it could be is `4000000` (64MB). Other values would be `2000000` (32MB), `1000000` (16MB), `800000` (8MB).

## Troubleshooting

1. "Couldn't find device"

You might have the open-source FTDI driver running, and that will conflict with FTDI's D2XX driver, which is used by this program. Check if the ftdi-sio module is loaded with the command ```lsmod | grep ftdi_sio``` and if it is, you can unload it with ```rmmod ftdi_sio```.

It may be obvious to some, but you may also need to ```sudo``` the ```64drive_usb``` command for the device to be accessed.

## License

Retroactive says it is "BSD-licensed."