# Quick Start
Assuming you already have GNURadio installed and the necessary OOT modules, then this is all you need to start running OreSat0.py:
```bash
git clone https://github.com/uniclogs/uniclogs-sdr.git
cd uniclogs-sdr/flowgraphs/
make
./OreSat0.py
```

* grcc OreSat0.grc will generate errors about \_\_file\_\_ in fix_relative_paths that can safely be ignored
* See `./OreSat0.py -h` for available arguments.
* By default, `./OreSat0.py` starts up sending TXEnable (sequence # 1) via L-band every 5 seconds
  * Use `-d` to set a different delay in ms between EDL commands
  * Use `-e none` to not automatically send any packets
    * `echo default= >> OreSat0.cfg` to do the same without the `-e` argument

# Transmitting
Depending on [which netcat](https://stackoverflow.com/a/75431334/7308581) you have, you will need different arguments to immediately exit after sending a UDP packet.

Assuming you are running GNU netcat, send a ping with:

```bash
xxd -r -p <<<c4f53822000900e501 | nc -cu 127.0.0.1 10025
```

# Setup

## Distros Supported
* Arch
* Debian Bookworm
* Ubuntu 22.04 "Jammy"

## Packages you'll neeed!
* Python 3
* gnuradio 3.10
* gr-satellites
* gr-osmosdr | gr-limesdr
* [gr-gpredict-doppler](https://github.com/ghostop14/gr-gpredict-doppler) (optional, but see GPredict section below)

## Basic setup starting from a fresh install of Ubuntu Server 22.04.4 LTS
```bash
# install radio specific package like uhd-host or hackrf described in sections below
sudo apt install gnuradio gr-satellites gr-osmosdr cmake rtl-sdr && volk_profile
git clone https://github.com/ghostop14/gr-gpredict-doppler
mkdir gr-gpredict-doppler/build
cd gr-gpredict-doppler/build/
cmake ..
make
sudo make install
cd -
git clone https://github.com/uniclogs/uniclogs-sdr.git
cd uniclogs-sdr/flowgraphs/
make
```

## GPredict
If you don't want to use GPredict for doppler correction, you can disable 5 blocks in the OreSat0.grc flowgraph rather than installing gr-gpredict. After opening the flowgraph with `gnuradio-companion OreSat0.grc`, look for and disable the 2 "GPredict Doppler" blocks and the 3 "Message Pair to Var" blocks connected to them. It is safe to leave gr-gpredict in even if you won't be using it.

## USRP B200 specific setup
```bash
sudo apt install uhd-host
sudo uhd_images_downloader
```

B200 serial numbers must be specified in the config file. Run `uhd_find_devices` to see the serial number of each connected device. Use your favorite editor to open `OreSat0.cfg` and find the `device_serial=` lines in the band sections. Example:

```bash
$ uhd_find_devices 2>&1 | grep serial
    serial: 32C7DEB
```

Set the serial number in `OreSat0.cfg`. `device_serial` needs to be set in multiple sections:
```ini
device_serial=32C7DEB
```

## HackRF specific setup
```bash
sudo apt install hackrf
```
If you will be using two HackRF radios, one for each transmit band, be sure to specify serial numbers in the config file. Run `hackrf_info` to see the serial number of each connected device. Use your favorite editor to open `OreSat0.cfg` and find the `device_serial=` line in the band sections. Any unique end of the serial number will do, but it is common to specify the last 4 bytes (8 hex characters). Example:

```bash
$ hackrf_info | grep Serial
Serial number: 0000000000000000088869dc385c721b
```

```ini
device_serial=385c721b
```

## Lime specific setup
Skip this section if you don't intend to use a Lime SDR.
```bash
sudo apt install gr-limesdr limesuite
```

* Test the Lime Driver
   * Run `LimeQuickTest`
   * Should be no errors
* Test your LimeMini
   * Run `LimeSuiteGUI`
   * Options > Connection Settings 
   * You should see your LimeSDR Mini -- double check the SN.
   * Choose it and click connect.
   * Click "Read Temp" in the upper right coerner and "Temperature:" Should swithc form ??? to a temp around 40 ish C.
   * Options > Connection Settings > Disconnect 
   * Quit!
* Load the INI file into the Lime RFE
   * Run `LimeSuiteGUI`
   * Modules > Lime RFE
   * Choose "Direct (USB)"
   * Choose Port > Open
   * Choose Configuration > Open  and chose the uniclogs-sdr/lime-rfe-setup.ini file
   * Quit!

