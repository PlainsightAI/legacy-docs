# Dell Edge Gateway 3001 + Sixgill Sense Ingress API Setup Guide

## Summary
This document details the process for setting up the Dell Edge Gateway 3001 and installing the Sixgill Sense Ingress API. Once configured, the gateway becomes a functioning part of the Sixgill Sense platform capable of ingesting data emitted by IoT devices and responding to client requests independently from the Sense Cloud. This integrated solution represents a significant milestone towards the strategic goal of positioning Sixgill Sense as the sensor data agnostic and edge processing capable software solution at the intersection of leading IoT vendors such as Dell, Intel and Ubuntu.

## Hardware Specifications:
- Ubuntu Core 16
- Intel® Atom™ E3805 1.33 GHz / 1 MB
- 2 GB DDR3L – 1066 MHz
- 8GB eMMC storage
- Wireless: Wireless LAN: 2.4GHz, 802.11b/g/n/Bluetooth Low Energy 4.0

## Device Setup

### Requirements
- LAN network with PoE Ethernet ports
- Wireshark Network Analyzer Software
- Level of Technical Expertise Recommended: **Intermediate**

### Setup Instructions
1. Acquire the gateway's Ethernet MAC address from the label included with the device.
2. With a computer connected to the LAN, open the Wireshark software and select the LAN-connected network adapter.
3. Set a filter in Wireshark to only display packets sent from the gateway's Ethernet MAC address formatted in Hex. The filter should look similar to the following: `eth.addr == 14:b3:1f:21:b9:e8`. Make sure the filter is applied.
4. Start the capture session and connect the gateway to the LAN via PoE enabled ethernet connection.
5. After a few seconds of network handshakes, a `DHCP Inform packet` will come across with a source IP address. That is the IP assigned to the gateway by the network. We will use that IP to SSH in to the device.
6. Use an SSH client to connect to the discovered device IP. The default credentials for the router is username: `admin`, password: `admin`. On Mac OS, this can be done through the terminal with the following command: `ssh admin@10.40.4.171`
7. If the SSH connection was successful an Ubuntu prompt will be opened and you are free to move on to the Software Setup section of this guide.

## Sixgill Sense Ingress API Ubuntu Core Setup (Developer v1.0)

In this section, we will take advantage of Ubuntu Core's built in package manager, `snap`, to install Docker which will handle the rest of the setup for us.

1. Install Docker using the following command: `sudo snap install docker`
2. Login to Docker to access Sixgill internal images. `sudo docker login`
3. Install a stable version of the Sixgill Sense Ingress API image from Docker Hub. As of the time of this writing, the preferred dev version is v0.1.9. `sudo docker pull sixgill/sense-api:v0.1.9`
4. Run the docker container to bring up the API server and start listening on port 5000 `sudo docker run -p 5000:5000 sixgill/sense-ingress-api:v0.1.9`
5. The API is live at `http://xx.xx.xx.xx:5000`. The Sixgill Sync mobile app cab be used as a test sensor to verify correct setup.
