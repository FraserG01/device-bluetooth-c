# Bluetooth Device Service
The Bluetooth Device Service connects Bluetooth
devices with EdgeX. The Device Service
currently limited to Bluetooth GATT devices/profiles.

## Prerequisites
- A Linux build host.
- [GCC][gcc] that supports C11.
- [Make][make] version 4.1.
- [CMake][cmake] version 3.1 or greater.
- [Cbor][libcbor] version 0.5.
- [Device-C-SDK][device-c-sdk] version 1.0.0 or greater.
- [D-Bus][dbus] version 1.12.2.
- [BlueZ][bluez] version 5.48.

## Configuration File

In the Device Service ```res/configuration.toml```
file, you will find two extra configurable options
under the `[Driver]` table.

```
[Driver]
  BluetoothInterface = "hci0"
  BluetoothDiscoveryDuration = 20
```

#### BluetoothInterface
BluetoothInterface specifies which Host Controller
Interface (HCI) the Device Service will use.
You can find out, which interfaces are
available with BlueZ ```hciconfig``` tool.

#### BluetoothDiscoveryDuration
When the Device Service starts, Bluetooth
discovery will be enabled for the time
amount specified by BluetoothDiscoveryDuration
(in seconds). After which, Bluetooth discovery
will be disabled.

## Adding A Device
To add a new Bluetooth device to the Device
Service, insert the layout below into the
configuration.toml file. Update the Name,
Profile, Description, Mac to match the device
you wish to add. Start the Device Service
and make sure that the device you're wanting
to add is in advertising mode, when the Device
Service starts.

```
[[DeviceList]]
  Name = "SensorTag-01"
  Profile = "CC2650 SensorTag"
  Description = "TI SensorTag 2650 STK"
  Labels = [ "bluetooth" ]
  [DeviceList.Protocols]
    [DeviceList.Protocols.Bluetooth]
      MAC = "00:00:00:00:00:00"
```

## Build
To build a local version of the Device Service,
clone the repository and run the following
shell commands.

Before building the device service, please
ensure that you have the EdgeX C-SDK installed and
make sure that the current directory is the device
service directory (device-bluetooth-c). To build
the device service, enter the command below into
the command line to run the build script.

```shell
$ ./scripts/build.sh
```

In the event that your C-SDK is not installed in the
system default paths, you may specify its location
using the environment variable CSDK_DIR.

After having built the device service, the executable
can be found at `build/{debug,release}/device-bluetooth-c/device-bluetooth-c`.

## Run
After successfully building the Device Service,
you should have the Device Service executable
in `./build/release/device-bluetooth-c/` named
`device-bluetooth-c`. To run this executable,
make sure your current directory is the root
project directory. As the Device Service
executable requires the configuration.toml in
`res/` and any device profiles in `profiles/`.

Run the following command in shell to start
the Device Service

```./build/release/device-bluetooth-c/device-bluetooth-c```


#### Command Line Options
|Option     | Shorthand Option  | Description                     |
|-----------|-------------------|:--------------------------------|
|--help     | -h                | Show help                       |
|--registry | -r                | Use the registry service        |
|--profile  | -p                | Set the profile name            |
|--confdir  | -c                | Set the configuration directory |

An example of using a command line option from the table above.
```./build/release/device-bluetooth-c/device-bluetooth-c -c res/```

## Docker

#### Build
To build a Docker image of the Device Service,
clone the repository and enter the following
shell commands.
```shell
$ cd device-bluetooth-c
$ docker build -t device-bluetooth \
  -f ./scripts/Dockerfile.alpine-3.9 \
  --build-arg arch=$(uname -m) .
```

#### Run
First, build a Docker image of the Device Service,
and enter the shell command below to run the Docker
image within a Docker container.

The Device Service docker container requires the
host machine `system_bus_socket` socket to be
mounted, to allow communication between the
container and the host machine D-Bus.

```shell
$ docker run \
  -v /var/run/dbus/system_bus_socket:/var/run/dbus/system_bus_socket \
  --privileged -p 49971:49971 device-bluetooth
```

[libcbor]: https://github.com/PJK/libcbor
[device-c-sdk]: https://github.com/edgexfoundry/device-sdk-c
[dbus]: https://www.freedesktop.org/wiki/Software/dbus/
[bluez]: http://www.bluez.org/
[make]: https://www.gnu.org/software/make/
[cmake]: https://cmake.org/
[gcc]: https://gcc.gnu.org/