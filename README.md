# airspy-fmradion

* Version 0.1.14, 24-FEB-2019
* Software decoder for FM broadcast radio with AirSpy
* For MacOS and Linux
* This repository is forked from [ngsoftfm-jj1bdx](https://github.com/jj1bdx/ngsoftfm-jj1bdx)

```sh
airspy-fmradion -q -t airspy \
    -c freq=88100000,srate=2500000,mgain=14,lgain=14,vgain=1 \
    -b 0.5 -R - | \
    play -t raw -esigned-integer -b16 -r 48000 -c 2 -q -
```

## Introduction

**airspy-fmradion** is a software-defined radio receiver for FM broadcast radio, specifically designed for Airspy R2, forked from [ngsoftfm-jj1bdx](https://github.com/jj1bdx/ngsoftfm-jj1bdx).

### Hardware supported

  - **Airspy** is supported with _libairspy_ library.

### Purposes of airspy-fmradion

Experimenting with digital signal processing and software radio, with a simpler profile than ngsoftfm-jj1bdx.

### airspy-fmradion provides

 - mono or stereo decoding of FM broadcasting stations
 - buffered real-time playback to soundcard or dumping to file
 - command-line interface (*only*)

### airspy-fmradion requires

 - Linux / macOS
 - C++11 (gcc, clang/llvm)
 - [Airspy library](https://github.com/airspy/host/tree/master/libairspy)
 - [sox](http://sox.sourceforge.net/)
 - tested: Airspy R2
 - Fast computer for Airspy
 - medium-strong FM radio signal.

For the latest version, see https://github.com/jj1bdx/airspy-fmradion

### Branches and tags

  - Official releases are tagged
  - _master_ is the "production" branch with the most stable release (often ahead of the latest release though)
  - _dev_ is the development branch that contains current developments that will be eventually released in the master branch
  - Other branches are experimental (and abandoned)

## Prerequisites

### Base requirements

  - `sudo apt-get install cmake pkg-config libusb-1.0-0-dev libasound2-dev libboost-all-dev`

### Airspy support

If you install from source (https://github.com/airspy/host/tree/master/libairspy) in your own installation path you have to specify the include path and library path. For example if you installed it in `/opt/install/libairspy` you have to add `-DAIRSPY_INCLUDE_DIR=/opt/install/libairspy/include -DHACKRF_LIBRARY=/opt/install/libairspy/lib/libairspy.a` to the cmake options.

To install the library from a Debian/Ubuntu installation just do:

  - `sudo apt-get install libairspy-dev`

### macOS

* Install HomeBrew `airspy`

## Installing

To install airspy-fmradion, download and unpack the source code and go to the
top level directory. Then do like this:

 - `mkdir build`
 - `cd build`
 - `cmake ..`

CMake tries to find librtlsdr. If this fails, you need to specify
the location of the library in one the following ways:

 - `cmake .. -DRTLSDR_INCLUDE_DIR=/path/rtlsdr/include -DRTLSDR_LIBRARY_PATH=/path/rtlsdr/lib/librtlsdr.a`
 - `PKG_CONFIG_PATH=/path/to/rtlsdr/lib/pkgconfig cmake ..`

Compile and install

 - `make -j8` (for machines with 8 CPUs)
 - `make install`

## Basic command options

 - `-t devtype` is mandatory and must be `rtlsdr` for RTL-SDR devices or `hackrf` for HackRF.
 - `-c config` Comma separated list of configuration options as key=value pairs or just key for switches. Depends on device type (see next paragraph).
 - `-d devidx` Device index, 'list' to show device list (default 0)
 - `-r pcmrate` Audio sample rate in Hz (default 48000 Hz)
 - `-M` Disable stereo decoding
 - `-R filename` Write audio data as raw S16_LE samples. Uuse filename `-` to write to stdout
 - `-W filename` Write audio data to .WAV file
 - `-P [device]` Play audio via ALSA device (default `default`). Use `aplay -L` to get the list of devices for your system
 - `-T filename` Write pulse-per-second timestamps. Use filename '-' to write to stdout
 - `-b seconds` Set audio buffer size in seconds
 - `-X` Shift pilot phase (for Quadrature Multipath Monitor) (-X is ignored under mono mode (-M))
 - `-U` Set deemphasis to 75 microseconds (default: 50)

## Modification by @jj1bdx

See [doc/NOTES-jj1bdx.md](doc/NOTES-jj1bdx.md) for the details of the modification.

* Remove 19kHz pilot signal when the stereo PLL is locked
* Add equalizer to compensate 0th-hold aperture effect of phase discriminator output
* The compensation equalizer output parameters are pre-calculated and interpolated from 200kHz ~ 10MHz sampling rates
* Increase the number of FineTuner table size from 64 to 256
* Add quiet mode `-q`
* Add option `-X` for [Quadratic Multipath Monitor (QMM)](http://ham-radio.com/k6sti/qmm.htm) (This option is not effective in monaural mode (`-M` option))
* Add option `-U` to set deemphasis timing to 75 microseconds for North America (default: 50 microseconds for Europe/Japan)

## Features remaining same as the original NGSoftFM

* IF bandwidth: 200kHz (+-100kHz)
* Default sample rate for RTL-SDR: 1000kHz

## Device type specific configuration options

### Airspy

  - `freq=<int>` Desired tune frequency in Hz. Valid range from 1M to 1.8G. (default 100M: `100000000`)
  - `srate=<int>` Device sample rate. `list` lists valid values and exits. (default `10000000`). Valid values depend on the Airspy firmware. Airspy firmware and library must support dynamic sample rate query.
  - `lgain=<x>` LNA gain in dB. Valid values are: `0, 1, 2, 3, 4, 5, 6, 7, 8 ,9 ,10, 11 12, 13, 14, list`. `list` lists valid values and exits. (default `8`)
  - `mgain=<x>` Mixer gain in dB. Valid values are: `0, 1, 2, 3, 4, 5, 6, 7, 8 ,9 ,10, 11 12, 13, 14, 15, list`. `list` lists valid values and exits. (default `8`)
  - `vgain=<x>` VGA gain in dB. Valid values are: `0, 1, 2, 3, 4, 5, 6, 7, 8 ,9 ,10, 11 12, 13, 14, 15, list`. `list` lists valid values and exits. (default `0`)
  - `antbias` Turn on the antenna bias for remote LNA (default off)
  - `lagc` Turn on the LNA AGC (default off)
  - `magc` Turn on the mixer AGC (default off)

## Authors

* Joris van Rantwijk
* Edouard Griffiths, F4EXB
* Kenji Rikitake, JJ1BDX

## Acknowledgments

* András Retzler, HA7ILM

## License

* As a whole package: GPLv3 (and later). See [LICENSE](LICENSE).
* Some source code files are stating GPL "v2 and later" license.
