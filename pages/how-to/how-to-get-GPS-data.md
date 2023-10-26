---
layout: default
title: How to the Blox location from GNSS with the IB-0310
parent: How to
nav_order: 7
has_children: false
has_toc: false
---

# How to the Blox location from GNSS with the IB-0310

The LTE modem that is mounted in the mPCIE slot with the IB-0310 includes a GNSS module. The location of the Blox device can be retreived from this module. To retreive the location data the `gpsd` needs to be configured first. 
`gpsd` is a daemon that acts as an intermediary between GPS (Global Positioning System) receivers and user applications. Once configured the location data can be displayed in the terminal or retrieved form software. 


## gpsd config

Before the `gpsd` can be used, proper configuration of the interface to the GNSS/GPS module needs to be done. 

The GNSS module which is included in the SIM7600E-H modem will use one of the exposed serial to USB connections. By default this is `/dev/ttyUSB1` for GNSS data and has a baudrate of 115200. 

Open the `gpsd` config file

```jsx
sudo nano /etc/default/gpsd
```

Set the correct interface for the GNSS under `DEVICES=` and also add the BAUDRATE=”115200”

```bash
# Devices gpsd should collect to at boot time.
# They need to be read/writeable, either by user gpsd or the group dialout.
DEVICES="/dev/ttyUSB1"
BAUDRATE="115200"
```

The file should look like this after the changes. 

```bash
# Default settings for the gpsd init script and the hotplug wrapper.

# Start the gpsd daemon automatically at boot time
START_DAEMON="true"

# Use USB hotplugging to add new USB devices automatically to the daemon
USBAUTO="true"

# Devices gpsd should collect to at boot time.
# They need to be read/writeable, either by user gpsd or the group dialout.
DEVICES="/dev/ttyUSB1"
BAUDRATE="115200"
# Other options you want to pass to gpsd
GPSD_OPTIONS=""
```

Now close and save the file. 

Before the new settings are used, the `gpsd` needs to restart or the system should reboot. 

```bash
sudo systemctl restart gpsd
```

## Retreiving GPS data in the Terminal

To display gps data in the terminal you can use `gpsmon` or `cgps` . 

### gpsmon

```bash
gpsmon
```

GPS data output

```bash
tcp://localhost:2947          NMEA0183>
┌──────────────────────────────────────────────────────────────────────────────┐o
│Time: 2023-10-26T11:51:38.000Z Lat:  50 50' 48.03224" Non:   4 21' 32.04234" E│v
└───────────────────────────────── Cooked TPV ─────────────────────────────────┘"
┌──────────────────────────────────────────────────────────────────────────────┐f
│ GLGSV BDGSV GPGSV GPGGA PQXFI GNGNS GPVTG GPRMC GPGSA GNGSA BDGSA PQGSA      │
└───────────────────────────────── Sentences ──────────────────────────────────┘
┌──────────────────┐┌────────────────────────────┐┌────────────────────────────┐
│Ch PRN  Az El S/N ││Time:      115138.00        ││Time:      115138.00        │
│ 0  86 277 26  47 ││Latitude:   5050.805374 N   ││Latitude:  5050.805374      │
│ 1  79  39 33  26 ││Longitude: 00421.540391 E   ││Longitude: 00421.540391     │
│ 2  69  99 18  32 ││Speed:     0.0              ││Altitude:  90.3             │
│ 3  87 334 19  36 ││Course:                     ││Quality:   1   Sats: 06     │
│ 4  71 302 37  43 ││Status:    A       FAA: A   ││HDOP:      0.9              │
│ 5  70  35 74   0 ││MagVar:    1.8  W           ││Geoid:     47.0             │
│ 6  73 174 18   0 │└─────────── RMC ────────────┘└─────────── GGA ────────────┘
│ 7  80 119 56   0 │┌────────────────────────────┐┌────────────────────────────┐
│ 8  85 232  6   0 ││Mode: A2 ...s: 86 79 69 87  ││UTC:           RMS:         │
│ 9   5 300 43  47 ││DOP: H=0.9   V=0.8   P=1.3  ││MAJ:           MIN:         │
│10   7  78 63  27 ││TOFF:  0.022214302          ││ORI:           LAT:         │
│11  11 223 27  30 ││PPS:                        ││LON:           ALT:         │
└────── GSV ───────┘└──────── GSA + PPS ─────────┘└─────────── GST ────────────┘
(75) $GPGGA,115138.00,5050.805374,N,00421.540391,E,1,06,0.9,90.3,M,47.0,M,,*55
(69) $PQXFI,115138.0,5050.805374,N,00421.540391,E,90.3,7.57,4.24,0.10*5C
(75) $GNGNS,115138.00,5050.805374,N,00421.540391,E,AAN,12,0.9,90.3,47.0,,,V*50
(34) $GPVTG,,T,1.8,M,0.0,N,0.0,K,A*04
(72) $GPRMC,115138.00,A,5050.805374,N,00421.540391,E,0.0,,261023,1.8,W,A*0B
(51) $GPGSA,A,2,05,07,11,13,20,30,,,,,,,1.3,0.9,0.8*31
(53) $GNGSA,A,2,05,07,11,13,20,30,,,,,,,1.3,0.9,0.8,1*32
(53) $GNGSA,A,2,69,71,79,80,86,87,,,,,,,1.3,0.9,0.8,2*3E
(41) $GNGSA,A,2,,,,,,,,,,,,,1.3,0.9,0.8,3*31
(30) $BDGSA,A,1,,,,,,,,,,,,,,,*0F
```

### cgps

`cgps` or client gps displays the full json message received from the GNSS module.

```bash
cgps
```

Output

```bash
┌───────────────────────────────────────────┐┌─────────────────────────────────┐
│    Time:       2023-10-26T11:57:51.000Z   ││PRN:   Elev:  Azim:  SNR:  Used: │
│    Latitude:    50.84676489 N             ││  69    16    102    24      Y   │
│    Longitude:    4.35901183 E             ││  71    40    305    45      Y   │
│    Altitude:   295.932 ft                 ││  73    21    174    29      Y   │
│    Speed:      0.00 mph                   ││  79    31    036    29      Y   │
│    Heading:    0.0 deg (true)             ││  80    58    113    18      Y   │
│    Climb:      0.00 ft/min                ││  86    24    274    43      Y   │
│    Status:     3D FIX (6 secs)            ││  87    20    331    36      Y   │
│    Longitude Err:   +/- 30 ft             ││  70    73    047    00      N   │
│    Latitude Err:    +/- 34 ft             ││  85    03    230    20      N   │
│    Altitude Err:    +/- 52 ft             ││                                 │
│    Course Err:      n/a                   ││                                 │
│    Speed Err:       +/- 47 mph            ││                                 │
│    Time offset:     0.022                 ││                                 │
│    Grid Square:     JO20eu                ││                                 │
└───────────────────────────────────────────┘└─────────────────────────────────┘
{"class":"VERSION","release":"3.17","rev":"3.17","proto_major":3,"proto_minor":12}

18Z","flags":1,"native":0,"bps":9600,"parity":"N","stopbits":1,"cycle":1.00}]}
{"class":"WATCH","enable":true,"json":true,"nmea":false,"raw":0,"scaled":false,"timing":false,"split24":false,"pps":false}
{"class":"SKY","device":"/dev/ttyUSB1","xdop":0.73,"ydop":0.86,"vdop":0.80,"tdop":1.06,"hdop":1.00,"gdop":2.56,"pdop":1.30,"satellites":[{"PRN":86,"el":24,"az":274,"ss":42,"used":true},{"PRN":73,"el":21,"az":174,"ss":30,"used":true},{"PRN":80,"el":58,"az":113,"ss":23,"used":true},{"PRN":79,"el":31,"az":36,"ss":27,"used":false},{"PRN":69,"el":16,"az":102,"ss":20,"used":true},{"PRN":87,"el":20,"az":331,"ss":35,"used":true},{"PRN":85,"el":3,"az":230,"ss":19,"used":false},{"PRN":71,"el":40,"az":305,"ss":46,"used":true},{"PRN":70,"el":73,"az":47,"ss":0,"used":false}]}
{"class":"TPV","device":"/dev/ttyUSB1","mode":3,"time":"2023-10-26T11:57:45.000Z","ept":0.005,"lat":50.846764767,"lon":4.359011333,"alt":90.200,"epx":10.961,"epy":12.938,"epv":18.400,"track":0.0000,"speed":0.000,"climb":-0.001,"eps":0.21,"epc":0.29}
{"class":"SKY","device":"/dev/ttyUSB1","xdop":0.84,"ydop":0.70,"vdop":0.70,"tdop":1.42,"hdop":0.80,"gdop":3.09,"pdop":1.10,"satellites":[{"PRN":86,"el":24,"az":274,"ss":43,"used":true},{"PRN":73,"el":21,"az":174,"ss":29,"used":true},{"PRN":80,"el":58,"az":113,"ss":23,"used":true},{"PRN":79,"el":31,"az":36,"ss":28,"used":true},{"PRN":69,"el":16,"az":102,"ss":21,"used":false},{"PRN":87,"el":20,"az":331,"ss":34,"used":true},{"PRN":85,"el":3,"az":230,"ss":19,"used":false},{"PRN":71,"el":40,"az":305,"ss":45,"used":true},{"PRN":70,"el":73,"az":47,"ss":0,"used":false}]}
{"class":"TPV","device":"/dev/ttyUSB1","mode":3,"time":"2023-10-26T11:57:46.000Z","ept":0.005,"lat":50.846764783,"lon":4.359011433,"alt":90.200,"epx":12.648,"epy":10.547,"epv":16.100,"track":0.0000,"speed":0.000,"climb":0.000,"eps":25.59,"epc":34.50}
```

## Python GPS data

`gps3` is Python package that can be used to retrieve the data from the `gpsd` daemon. Below is a simple example on how to retrieve the data

First install the `gps3` package

```bash
pip3 install gps3
```

GPS date example

```bash
from gps3 import gps3
gps_socket = gps3.GPSDSocket()
data_stream = gps3.DataStream()
gps_socket.connect()
gps_socket.watch()
for new_data in gps_socket:
    if new_data:
        data_stream.unpack(new_data)
        print('Alt = ', data_stream.TPV['alt'])
        print('Lat = ', data_stream.TPV['lat'])
        print('Lon = ', data_stream.TPV['lon'])
```
