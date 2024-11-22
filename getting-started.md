

# Setup steps on Windows 11 + WSL2
## 1. Download and install:
- CP210x driver:
https://www.silabs.com/documents/public/software/CP210x_Universal_Windows_Driver.zip

- USBPID-windows:
https://github.com/dorssel/usbipd-win
https://github.com/dorssel/usbipd-win/releases/tag/v4.3.0


## 2. Check/share device via usbpid in PowerShell (admin mode needed)
```
usbipd --help
usbipd list
```

Search like:
`14-1 | 10c4:ea60 | Silicon Labs CP210x USB to UART Bridge(COM5) | Not shared`

First value is the BUSID - memorize it! Sharing a device is **persistent**. It survives reboots!
```
usbipd bind --busid <BUSID>
example cmd: usbipd bind --busid 14-1

usbipd attach --wsl --busid=<BUSID>
example cmd: usbipd attach --wsl Ubuntu-24.04 --busid 14-1
```

## WSL2 - Ubuntu

Check sharing in the distro: `ll /dev/tty*`
Should find like: `crw-rw---- 1 root dialout 188,  0 Nov 22 15:03 /dev/ttyUSB0`

Open VSCode Check `.devcontainer/devcontainer.json` `"runArgs": ["--device=/dev/ttyUSB0"],` and adapt the device is needed

Rebuild/Open is container

Check mapping/device is reachable: `esptool.py --port /dev/ttyUSB0 flash_id`
You should see something similar:
```
esptool.py --port /dev/ttyUSB0 flash_id
esptool.py v4.8.1
Serial port /dev/ttyUSB0
Connecting....
Detecting chip type... Unsupported detection protocol, switching and trying again...
Connecting.......
Detecting chip type... ESP32
Chip is ESP32-D0WD-V3 (revision v3.1)
Features: WiFi, BT, Dual Core, 240MHz, VRef calibration in efuse, Coding Scheme None
Crystal is 40MHz
MAC: b0:b2:1c:a6:34:44
Uploading stub...
Running stub...
Stub running...
Manufacturer: 5e
Device: 4016
Detected flash size: 4MB
Flash voltage set by a strapping pin to 3.3V
Hard resetting via RTS pin...
```