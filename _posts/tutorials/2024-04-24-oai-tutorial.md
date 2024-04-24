---
layout: post
title: "Deploying OpenAirInterface (OAI) 5G RAN and Core-network in Colosseum"
date: 2024-04-24
category: tutorials
author:
short-description: How to deploy the latest version of OAI gNB, UE and 5G Core network in Colosseum and establish end-to-end connection
---

There are ready-made images in Colosseum NAS which include OAI RAN and 5G Core Network. These images can be selected in the Colosseum reservation page under the `common` section. The images of interest are

 - RAN Image: `oai-ran-2024-w13`
 - Core-network Image: `oai-5gcore-2024`

Make a reservation with `n+2` SRNs out of which one Core-network image and `n+1` RAN Images where `n` is the number of UEs in the test scenario.

Once the reservation is active, you can login to the SRNs. The password of `root` user is `pass`.

## Run OAI 5G Core-network
In SRN running `oai-5gcore-2024`
```
cd oai-cn5g
docker compose up -d
```
This should start all the required docker containers for running the core-network.
## Run OAI gNB
The image contains a python script to start the gNB and UE.
```
cd OAI-Colosseum
python3 ran.py -t donor -m sa
```
This will start the gNB process. If you get an error related to FPGA compatibility number mismatch, run the above command with `-f` option. This will flash the FPGA of the USRP with the version that matches your version of the UHD driver. This option is only needed once. The subsequent runs can be started without `-f`.
## Start RF Scenario
Now we need to tell the MCHEM (Massive CHannel EMulator) to set up a channel between all the nodes in the current reservation. This can be done from any of the SRNs in the reservation. The following command starts an RF scenario with 0 dB path loss. (Please note that losses due to hardware impairments are still present.)

`colosseumcli rf start -c 10011`

## Run OAI UE
Run the following commands.
```
cd OAI-Colosseum
python3 ran.py -t ue -m sa
```
If all goes well, the UE should connect to the gNB and the core network should have assigned an IP address to the UE. This can be verified by running `ifconfig` on the UE side and checking if the network interface named `oaitun_ue` has an IP assigned.

Sometimes the UE may continuously fail the Random Access (RA) procedure and not proceed beyond that point. If this happens, try changing the `timing_advance` value in the `106` PRB section of the `conf.json` file (increase or decrease in steps of 5).
## IP Traffic
Ping the core-network from UE using `ping 192.168.70.135 -I oaitun_ue`. If the ping is successful, it means that an end-to-end connection between the UE and the 5G core network has been established. 
The user can push more traffic over the RAN using tools such as `iperf3`, or equivalent.
On the core-network side, run `docker exec -it oai-ext-dn iperf3 -s`
### Downlink Traffic
On the UE side, run `iperf3 -c 192.168.70.135 -B <ip of oaitun_ue> -b 50M -u -R`
### Uplink Traffic
On the UE side, run `iperf3 -c 192.168.70.135 -B <ip of oaitun_ue> -b 15M -u`
