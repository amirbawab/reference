## Raspberry Pi

### Connect to RPI using Serial
#### Enable Serial on RPI
* Power up the RPI and connect it to a monitor (e.g. using HDMI)
* Login with default username `pi` and password `raspberry`
* After logging in, enable Serial communication. Type `sudo raspi-config`, then go to `Interfacing` > `Serial`, and select `Yes`.
* Shutdown the RPI: `sudo shutdown now`
* Done!

#### Connect to RPI
* Plug an FTDI basic (e.g. SparkFun FTDI basic) or FTDI cable (e.g. SparkFun FTDI cable) into your machine. Use `dmesg` to know the device file name (e.g. /dev/ttyUSB0)
* Wire the FTDI device into the RPI by connecting the FTDI RX into the RPI TX and the FTDI TX into the RPI RX.
* Connect to the FTDI device using the `screen` command (install it if not found): `sudo screen /dev/ttyUSB0 115200` where `/dev/ttyUSB0` is the device file name found above, and `115200` is the baud rate.
* Power up the RPI
* Done! Now you can interact with the RPI.
