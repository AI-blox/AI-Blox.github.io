---
layout: default
title: How to connect to LTE
parent: How to
nav_order: 6
has_children: false
has_toc: false
---

# How to connect your Blox to LTE

A Blox equiped with the CB-02x0 module has an LTE modem mounted into the mPCIE slot. The sections below explain how to setup the connection to the network operator of a SIM card. 


Pre-requisites is that you have a SIM card available and it is [installed in the Blox SIM card holder.](pages\how-to\how-to-1.html)

## Find LTE modem with ModemManager

Before setting up the connection, check if the LTE Modem is recognised by the Linux ModemManager. 

```bash
 mmcli -L
```
Output
```bash
    /org/freedesktop/ModemManager1/Modem/0 [SIMCOM INCORPORATED] SIMCOM_SIM7600E-H
```
The output shows that the LTE modem is recognised, mapped byt the ModemManager as modem `0`. Also the brand and type of the modem is displayed. In this example is a SIM7600E-H. 

Now more information and status of the modem can be requested using the ModemManager. The output of the previous command indicated that the modem was recognised as modem `0`` by the ModemManager on the following path `/org/freedesktop/ModemManager1/Modem/0`. 

```bash
sudo mmcli -m 0
```
Output
```bash
ai-blox@tegra-ubuntu:~$ sudo mmcli -m 0
[sudo] password for ai-blox:
  --------------------------------
  General  |            dbus path: /org/freedesktop/ModemManager1/Modem/0
           |            device id: 94a0c1f62c95060d1f5090d5756011433ea3c26d
  --------------------------------
  Hardware |         manufacturer: SIMCOM INCORPORATED
           |                model: SIMCOM_SIM7600E-H
           |             revision: LE11B12SIM7600M22
           |            supported: gsm-umts, lte
           |              current: gsm-umts, lte
           |         equipment id: xxxxxxxxxxxxxxx
  --------------------------------
  System   |               device: /sys/devices/3610000.xhci/usb1/1-2/1-2.2
           |              drivers: option1
           |               plugin: SimTech
           |         primary port: ttyUSB2
           |                ports: ttyUSB0 (qcdm), ttyUSB2 (at), ttyUSB3 (at)
  --------------------------------
  Status   |       unlock retries: sim-pin (3), sim-pin2 (3), sim-puk (10), sim-puk2 (10)
           |                state: registered
           |          power state: on
           |       signal quality: 80% (recent)
  --------------------------------
  Modes    |            supported: allowed: 2g; preferred: none
           |                       allowed: 3g; preferred: none
           |                       allowed: 2g, 3g; preferred: none
           |                       allowed: 2g, 3g; preferred: 2g
           |                       allowed: 2g, 3g; preferred: 3g
           |                       allowed: 2g, 3g, 4g; preferred: none
           |              current: allowed: any; preferred: none
  --------------------------------
  IP       |            supported: ipv4, ipv6, ipv4v6
  --------------------------------
  3GPP     |                 imei: xxxxxxxxxxxxxxx
           |          operator id: 20601
           |        operator name: Proximus
           |         registration: home
  --------------------------------
  3GPP EPS | ue mode of operation: csps-2
  --------------------------------
  SIM      |            dbus path: /org/freedesktop/ModemManager1/SIM/0
```
The output shows that the LTE modem is registered on the LTE network (`state: registered`) of the operator (`operator name: Proximus`). The operator is of course linked to the used SIM card. Also displayed is the signal quality, in this case 80%. 
At this stage the modem is registered on the network, this does not mean it is connected to the network. The modem is recognised by the network and can connect to it. To be able to connect, a connection in the network manager needs to be created.

## Creating a network connection

Before adding a new connection, a check can be done if there is already a connection available. 

```bash
nmcli connection show
```
Outptu
```bash
ai-blox@tegra-ubuntu:~$ nmcli connection show
NAME                UUID                                  TYPE      DEVICE
Wired connection 1  43f7c732-05c1-3c66-b2da-4a2a0cf7ae3d  ethernet  eth0
docker0             87279c56-a9d9-437c-a305-469abda47fba  bridge    docker0
```
The output shows that there is not yet a `ppp0` connection for a device on `ttyUSB2` created. It needs to be added. 

To add a new connection, the APN or Access Point Name of the LTE operator needs to be known. In the example below the APN for Proximus is `internet.proximus.be`. This will be different for other LTE operators. 

Following arguments will be used to add the new connection

- `con-name` or name of the connection can be anything, in this example it is `BloxLTE`
- `ifname` is the main port of the device, in this case as mentioned above `ttyUSB2`
- `apn` or access point name, provided by the telecom operator. In this example Proximus is the operator and their apn is `“internet.proximus.be”`


```bash
sudo nmcli connection add type gsm con-name "BloxLTE" ifname ttyUSB2 apn "internet.proximus.be"
```
Output
```bash
Connection 'BloxLTE' (74c742c0-afd1-428a-82dc-b5d892dfd840) successfully added.
```

Once the connection successfully has been added, check the known connections

```bash
nmcli connection show
```
Output
```bash
ai-blox@tegra-ubuntu:~$ nmcli connection show
NAME                UUID                                  TYPE      DEVICE
BloxLTE             74c742c0-afd1-428a-82dc-b5d892dfd840  gsm       ttyUSB2
Wired connection 1  43f7c732-05c1-3c66-b2da-4a2a0cf7ae3d  ethernet  eth0
docker0             87279c56-a9d9-437c-a305-469abda47fba  bridge    docker0
```
If in the output the BloxLTE color font is green, it means the connection is active and can be used. 

When the connection has been added a PPP interface also has been created. This can be checked with `ifconfig`.


```bash
ifconfig
```
Output
```bash
ifconfig
docker0: flags=4099<UP,BROADCAST,MULTICAST>  mtu 1500
        inet 172.17.0.1  netmask 255.255.0.0  broadcast 172.17.255.255
        ether 02:42:78:3b:05:9b  txqueuelen 0  (Ethernet)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.1.49  netmask 255.255.255.0  broadcast 192.168.1.255
        inet6 2a02:a03f:6997:a000:ed9f:df03:8d77:1299  prefixlen 64  scopeid 0x0<global>
        inet6 fe80::5ba1:b1d4:767f:565e  prefixlen 64  scopeid 0x20<link>
        inet6 2a02:a03f:6997:a000:68cb:eaab:37c5:9d42  prefixlen 64  scopeid 0x0<global>
        ether 48:b0:2d:3d:f0:0e  txqueuelen 1000  (Ethernet)
        RX packets 2491  bytes 753339 (753.3 KB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 925  bytes 108284 (108.2 KB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
        device interrupt 150  base 0x7000

eth1: flags=4099<UP,BROADCAST,MULTICAST>  mtu 1500
        ether 0a:2f:50:16:9f:b1  txqueuelen 1000  (Ethernet)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1  (Local Loopback)
        RX packets 232  bytes 20680 (20.6 KB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 232  bytes 20680 (20.6 KB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

ppp0: flags=4305<UP,POINTOPOINT,RUNNING,NOARP,MULTICAST>  mtu 1500
        inet 100.73.125.102  netmask 255.255.255.255  destination 0.0.0.0
        ppp  txqueuelen 3  (Point-to-Point Protocol)
        RX packets 19  bytes 981 (981.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 22  bytes 965 (965.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

rndis0: flags=4099<UP,BROADCAST,MULTICAST>  mtu 1500
        ether d2:95:a0:9e:8f:6d  txqueuelen 1000  (Ethernet)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

usb0: flags=4099<UP,BROADCAST,MULTICAST>  mtu 1500
        ether d2:95:a0:9e:8f:6f  txqueuelen 1000  (Ethernet)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

```
Amongst other available interfaces, also Point-to-Point `ppp0` is shwon with its ap address from the LTE operator. 

```bash
ppp0: flags=4305<UP,POINTOPOINT,RUNNING,NOARP,MULTICAST>  mtu 1500
        inet 100.73.125.102  netmask 255.255.255.255  destination 0.0.0.0
        ppp  txqueuelen 3  (Point-to-Point Protocol)
```

To test, Google (8.8.8.8) can be pinged over the `ppp0` interface.

```bash
ping -I ppp0 -c 10 8.8.8.8
```

```bash
ai-blox@tegra-ubuntu:~$ ping -I ppp0 -c 10 8.8.8.8
PING 8.8.8.8 (8.8.8.8) from 100.73.125.102 ppp0: 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=115 time=124 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=115 time=31.8 ms
64 bytes from 8.8.8.8: icmp_seq=3 ttl=115 time=31.8 ms
64 bytes from 8.8.8.8: icmp_seq=4 ttl=115 time=30.1 ms
64 bytes from 8.8.8.8: icmp_seq=5 ttl=115 time=27.8 ms
64 bytes from 8.8.8.8: icmp_seq=6 ttl=115 time=36.2 ms
64 bytes from 8.8.8.8: icmp_seq=7 ttl=115 time=34.6 ms
64 bytes from 8.8.8.8: icmp_seq=8 ttl=115 time=33.7 ms
64 bytes from 8.8.8.8: icmp_seq=9 ttl=115 time=31.7 ms
64 bytes from 8.8.8.8: icmp_seq=10 ttl=115 time=28.5 ms

--- 8.8.8.8 ping statistics ---
10 packets transmitted, 10 received, 0% packet loss, time 9014ms
rtt min/avg/max/mdev = 27.859/41.146/124.876/28.017 ms
ai-blox@tegra-ubuntu:~$
```
