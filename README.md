[![verified-by-homebridge](https://badgen.net/badge/homebridge/verified/purple)](https://github.com/homebridge/homebridge/wiki/Verified-Plugins)
![node](https://img.shields.io/node/v/homebridge-daikin-local)
[![npm](https://img.shields.io/npm/dt/homebridge-daikin-local.svg)](https://www.npmjs.com/package/homebridge-daikin-local)
[![npm](https://img.shields.io/npm/l/homebridge-daikin-local.svg)](https://github.com/cbrandlehner/homebridge-daikin-local/blob/master/LICENSE)
[![npm version](https://badge.fury.io/js/homebridge-daikin-local.svg)](https://badge.fury.io/js/homebridge-daikin-local)

![Node.js CI](https://github.com/cbrandlehner/homebridge-daikin-local/workflows/Node.js%20CI/badge.svg)
![CodeQL](https://github.com/cbrandlehner/homebridge-daikin-local/workflows/CodeQL/badge.svg)
[![HCL AppScan CodeSweep](https://github.com/cbrandlehner/homebridge-daikin-local/actions/workflows/HCL_AppScan_CodeSweep.yml/badge.svg)](https://github.com/cbrandlehner/homebridge-daikin-local/actions/workflows/HCL_AppScan_CodeSweep.yml)
# homebridge-daikin-local

Supports Daikin Air Conditioners on [HomeBridge](https://github.com/nfarina/homebridge) by connecting to the optional [Daikin Wifi Controller](https://amzn.to/2MZDQjg).


<img src="https://user-images.githubusercontent.com/2294359/80783655-abb6c200-8ba4-11ea-9b60-d5823e3b788f.jpeg" align="center" alt="controller" style="transform:rotate(90deg);" width="50%" height="50%">

<img src="https://user-images.githubusercontent.com/2294359/80783675-b4a79380-8ba4-11ea-9fa8-f48f9bf12585.jpeg" align="center" alt="controller" width="50%" height="50%">


# Installation
cd /home/pi
git clone https://github.com/Mich-b/homebridge-daikin-local
npm install
sudo npm link



This plugin retrieves sensor and mode data from a [Daikin WIFI controller](https://amzn.to/2MZDQjg) in your local network and allows you to set operation modes and target temperatures. As it is a plugin for [HomeBridge](https://github.com/nfarina/homebridge) you will have access to this features using Apple Home.

The install may require you to run as an administrator (using a different login or sudo).
It is recommended to configure your DHCP server to reserve an IP for the wifi controller.
This plugin can be installed using the [Homebridge Config UI X](https://github.com/oznu/homebridge-config-ui-x#readme) or manually by following these steps:

1. Install homebridge using: npm install -g homebridge
2. Install this plugin using: npm install -g homebridge-daikin-local
3. Update your configuration file. See sample-config.json in this repository for a sample.


# Configuration

Configuration sample:

 ```
    {
        "bridge": {
            ...
        },

        "description": "...",

        "accessories": [
            {
                "accessory": "Daikin-Local",
                "name": "Living room",
                "apiroute": "http://192.168.1.50",
                "temperature_unit": "C",
                "system": "Default",
                "swingMode": "2",
                "defaultMode": "0",
                "fanMode": "FAN",
                "fanName": "Living room FAN"
            }
        ],

        "platforms":[]
    }
```

This screenshot shows the configuration in [Homebridge Config UI X](https://github.com/oznu/homebridge-config-ui-x#readme):

<img src="https://user-images.githubusercontent.com/10800971/80524996-daf4e580-8990-11ea-9e13-3328a65f20af.png" align="center" alt="configuration" width="50%" height="50%">


# Features

The FAN:
The FAN allows you to turn on the fan of your Daikin AC.
You can also set the speed of the fan. Apple HomeKit allows you to set a speed percentage from 0% to 100%.
This plugin translates this percentage value as follows:
0% to 9%: SILENT mode
10% to 20%: AUTO mode
21% to 30%: Level 3
31% to 40%: Level 4
41% to 60%: Level 5
61% to 80%: Level 6
81% to 100%: Level 7 (max)

The AC:
Apple HomeKit settings allow you to enable or disable the swing aka oscillation mode. As HomeKit is limited to a true or false value, the plugins configuration allows you to configure the type of swing mode. Available modes are: horizontal swing, vertical swing and 3D.

<img src="https://user-images.githubusercontent.com/2294359/80783674-b40efd00-8ba4-11ea-9977-5af6bdc5799c.png" align="center" alt="Aircon" width="50%" height="50%">


# Technical background information on the API being used

The `apiroute` is used for two main calls: Get info such as current activity and sensor readings from the thermostat and set the target temperature and modes. The Aircon LAN adapter provides two directories for these settings and data:

1. `/common` uses the GET method for control and system information about the Aircon (e.g software version, MAC address, Reboot System, Region)

2. `/aircon` uses the GET method to set Aircon related information (e.g Target Temperature, Modes like Heat and Cool, Temperature Sensor Readings, Timers)

# Supported devices

Currently, this plugin supports Daikin wifi controllers supporting the "aircon" URLs (System: Default) and "skyfi" URLs (System: Skyfi).

To test `http` connectivity, use your browser to connect to your device using one of these URLs:
 ```
http://192.168.1.88/aircon/get_model_info
http://192.168.1.88/skyfi/aircon/get_model_info
 ```
replace the IP (192.168.1.88) with the IP of your device.

Your browser should return a line like this:
 ```
ret=OK,model=0AB9,type=N,pv=2,cpv=2,cpv_minor=00,mid=NA,humd=0,s_humd=0,acled=0,land=0,elec=0,temp=1,temp_rng=0,m_dtct=1,ac_dst=--,disp_dry=0,dmnd=0,en_scdltmr=1,en_frate=1,en_fdir=1,s_fdir=3,en_rtemp_a=0,en_spmode=0,en_ipw_sep=0,en_mompow=0
 ```
If it does not, your device is not yet supported.

To test `https` connectivity see [HTTPS/Registered client support](#https-registered-client)

The response of an usupported device will look like this:
 ```
ret=PARAM NG,msg=404 Not Found
 ```

Tested devices:

Daikin BRP069A41, Model: 0ABB, Firmware: 3.3.6

Daikin BRP069B41, Model: 0AB9, Firmware: 1.2.51

Daikin BRP069B45, Model: 0000, Firmware: 1.2.51

Daikin BRP072C42, Model: 0000, Firmware: 1.13.7 (requires https and token)

Daikin Emura BRP072A43, model 10C8, firmware 2.9

Daikin Emura BRP072A43, model 10C9, firmware 2.9

Daikin Siesta ATXP-K3, (model unknown), Firmware 3.3.6

Daikin Cora BRP072A42, Model 0C87, Firmware 3.4.3

Daikin Cora BRP072A42, Model 0C89, Firmware 3.4.3

Daikin Cora BRP072A42, Model 0C8A, Firmware 3.4.3

Daikin Cora BRP072A42, Model 0C8B, Firmware 3.4.3

Daikin Cora BRP072A42, Model 0EB0, Firmware 3.4.3

Daikin FDXM-F3, Model: FDXM35F3V1B, Firmware 3.3.6

Daikin FKXS20, Model: BRP069A43, Firmware 1.2.51

Daikin FTXA20 (Stylish), Model: FTXA20A2V1BW, Firmware 1.2.51


If you have other devices or firmware versions working, please let me know so I can update the list of tested devices.

## HTTPS/Registered client support<a id="https-registered-client"></a>

Some models require requests via `https` containing a registered client token.

It is necessary to register a client token with each device.
The same token may be registered with multiple devices.

These instructions are based on comments in [GitHub Project ael-code/daikin-control Issue #27](https://github.com/ael-code/daikin-control/issues/27)

1. Generate a UUID4 (https://www.uuidgenerator.net can be used), _e.g._ `7b9c9a47-c9c6-4ee1-9063-848e67cc7edd`
2. Strip the `-` from the UUID, _i.e._ `7b9c9a47c9c64ee19063848e67cc7edd`
3. Grab the 13-digit key from the sticker on the back of the controller. _e.g._ `0123456789012`
4. Register the UUID as a client token
```
curl --insecure -H "X-Daikin-uuid: 7b9c9a47c9c64ee19063848e67cc7edd" -v "https://<controller-ip>/common/register_terminal?key=0123456789012"
```

This UUID must be used in client requests to the device.

Test your registered token using the above requests but using `https` instead of `http`, _e.g._
```
curl --insecure -H "X-Daikin-uuid: 7b9c9a47c9c64ee19063848e67cc7edd" -v "https://192.168.1.88/aircon/get_model_info"
curl --insecure -H "X-Daikin-uuid: 7b9c9a47c9c64ee19063848e67cc7edd" -v "https://192.168.1.88/skifi/aircon/get_model_info"
```

In the configuration file, make sure you specify `https` in the `apiroute` option
and add the registered token as the value of `uuid` in the configuration for _each_ device, _e.g._
```
        "accessories": [
            {
                "accessory": "Daikin-Local",
                "name": "Living room",
                "apiroute": "https://192.168.1.50",
                "uuid": "7b9c9a47c9c64ee19063848e67cc7edd",
                "system": "Default",
                "swingMode": "2",
                "defaultMode": "0",
                "fanMode": "FAN",
                "fanName": "Living room FAN"
            }
        ],
```
Make sure to use the correct token if a different token has been registered with each device.

# Debugging and Testing

This plugins code makes heavy use of debug output. Normally, this debug output is not visible on the [homebridge](https://github.com/nfarina/homebridge) console.
As explained in the [Homebridge troubleshooting documentation](https://github.com/nfarina/homebridge/wiki/Basic-Troubleshooting) you should start [homebridge](https://github.com/nfarina/homebridge) like this to see the debug output:

```
homebridge -D
```

For even more debug, use this:

```
DEBUG=* homebridge -D
```

# Credit

This remake is based on homebridge-daikin. Since it's no longer maintained, [cbrandlehner](https://github.com/cbrandlehner/) forked the project.
