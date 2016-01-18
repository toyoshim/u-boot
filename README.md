# U-Boot temporary fork to support versatile
## Purpose
Revive ARM versatile platform support to make u-boot available in QEMU arm system emulation.

## Status
I didn't verified well yet, but "loads" and "tftp" command can load u-boot standalone applications and "go" command runs the loaded application.

## Build
```
% sudo apt-get install gcc-arm-none-eabi  # for debian and variants
% git clone https://github.com/toyoshim/u-boot.git
% cd u-boot
% make versatile_config CROSS_COMPILE=arm-none-eabi-
% make all CROSS_COMPILE=arm-none-eabi-
```

## Run
```
% qemu-system-arm -M versatilepb -m 256M -nographic -kernel u-boot.bin


U-Boot 2016.01-g4da311b (Jan 18 2016 - 21:16:04 +0900)

DRAM:  128 MiB
WARNING: Caches not enabled
Using default environment

In:    serial
Out:   serial
Err:   serial
Net:   SMC91111-0
Versatile # loads
## Ready for S-Record download ...
(paste contents of examples/standalone/hello_world.srec from stdin here)
## First Load Addr = 0x0C100000
## Last  Load Addr = 0x0C10026D
## Total Size      = 0x0000026E = 622 Bytes
## Start Addr      = 0x0C100000
Versatile # go 0x0c100000
## Starting application at 0x0C100000 ...
Example expects ABI version 8
Actual U-Boot ABI version 8
Hello World
argc = 1
argv[0] = "0x0c100000"
argv[1] = "<NULL>"
Hit any key to exit ... 
```

If you are ready to use tap and tftp, it would work somehow like this.

```
% sudo qemu-system-arm -M versatilepb -m 256M -nographic -net nic -net tap -kernel u-boot.bin
(snip)
Versatile # setenv serverip <tftp server ip, e.g. 192.168.0.1>
Versatile # setenv ipaddr <qemu nic ip, e.g. 192.168.0.2>
Versatile # tftp 0x0c100000 /hello_world.bin
SMC91111: PHY auto-negotiate timed out
SMC91111: MAC 52:54:00:12:34:56
Using SMC91111-0 device
TFTP from server 192.168.12.57; our IP address is 192.168.12.234
Filename '/hello_world.bin'.
Load address: 0xc100000
Loading: #
	 0 Bytes/s
done
Bytes transferred = 622 (26e hex)
Versatile # go 0x0c100000
## Starting application at 0x0C100000 ...
Example expects ABI version 8
Actual U-Boot ABI version 8
Hello World
argc = 1
argv[0] = "0x0c100000"
argv[1] = "<NULL>"
Hit any key to exit ... 
```

Note: Standalone applications are built so that it starts from 0x0c100000. It means 128MB of main memory isn't enough to run them.
