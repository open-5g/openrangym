---
layout: post
title: "Use of SCOPE and ColO-RAN near-RT RIC"
date: 2022-11-11
category: tutorials
author:
short-description: How to setup and use SCOPE and ColO-RAN near-RT RIC with Colosseum
---

{% assign publications = site.data.publications %}

In this tutorial, we show how to use the [SCOPE](ran-frameworks/scope) and [ColO-RAN](o-ran-frameworks/coloran) components of OpenRAN Gym to develop O-RAN-compliant Artificial Intelligence (AI)/Machine Learning (ML) solutions, deploy them as xApps on the near-RT RAN Intelligent Controller (RIC), and test them on large-scale softwarized Radio Access Network (RAN) deployed on the [Colosseum](experimental-platforms/colosseum) wireless network emulator and controlled by the RIC.

#### Prerequisites
In the remaining of this tutorial, we assume users started an interactive reservation on Colosseum with one ColO-RAN near-RT RIC node (`coloran-near-rt-ric` or `coloran-near-rt-ric-prebuilt` image), and at least one SCOPE node (`scope-with-e2` image).


### Starting the SCOPE Base Station
As a first step, we start the SCOPE base station and pass the configuration of the base station through the `radio.conf` file. In the SCOPE node, type the following commands. These commands take care of starting the base station and core network applications. They can also be used on other nodes to start the UEs applications.

{% highlight bash %}
cd radio_api/
python3 scope_start.py --config-file radio.conf
{% endhighlight %}


### Starting the ColO-RAN Near-RT RIC
Now we start the ColO-RAN near-RT RIC. In the ColO-RAN node, type the following commands. These commands will build and start the Docker images of the ColO-RAN near-RT RIC, and specify the network interface used by the RIC to communicate with the RAN (e.g., the `col0` interface in Colosseum).

{% highlight bash %}
cd setup-scripts/
./setup-ric.sh col0
{% endhighlight %}

First, base Docker images, used to build the RIC images, are imported. Then, the four images composing the near-RT RIC are built, and their IP addresses and ports (defined in the `setup-lib.sh` script) are configured. These images include:

- `e2term`, which is the endpoint of the E2 messages on the RIC
- `e2mgr`, which manages the messages to/from the E2 interface
- `e2rtmansim`, which leverages the RIC Message Router (RMR) protocol to route the E2 messages inside the RIC
- `db`, which maintains a database of the RAN nodes connected to the RIC

After the building process completes, the Docker images are initialized as containers on a Colosseum Standard Radio Node (SRN), and listen for incoming connections from RAN nodes implementing an E2 termination endpoint. The container logs can be read through the `docker logs` command, e.g., `docker logs e2term -f` shows the logs of the E2 termination (`e2term`) container.


### Connecting the SCOPE Base Station to ColO-RAN Near-RT RIC
After the ColO-RAN neat-RT RIC is started following the previous steps, the SCOPE base station can be connected to it. To this aim, the SCOPE base station runs an instance of the O-RAN E2 termination, which we adapted from the O-RAN Software Community (OSC) Distributed Unit (DU) implementation. After connecting to the near-RT RIC, this component can exchange messages with the xApps running therein. Specifically, the E2 termination of the base station can:

- receive RIC Subscription messages from the xApps
- send periodic RAN Key Performance Measurements (KPMs) to the xApps through RIC Indication messages
- receive RIC Control messages from the xApps
- interface with the SCOPE Application Programming Interfaces (APIs) to modify the scheduling and slicing configurations of the base station based on the received xApp control

We initialize the E2 termination on the SCOPE base station by typing the following commands on the SCOPE node.

{% highlight bash %}
cd colosseum-scope-e2/
./build_odu.sh clean
./run_odu.sh
{% endhighlight %}

The E2 termination is first built through the `build_odu.sh` script, which also sets the IP address and port of the near-RT RIC to connect to, as well as the network interface used for the connection to the RIC. Then, the E2 termination can be started through the `run_odu.sh` script, which initializes the E2 termination and connects it to the near-RT RIC. The successful connection of base station and near-RT RIC can be verified by reading the logs of the `e2term` container on the ColO-RAN node (e.g., through the command `docker logs e2term -f`). This log shows the association messages between the RIC and the base station, together with the ID of the connected base stations (e.g., `gnb:311-048-01000501`).


### Initializing a Sample xApp
After starting the near-RT RIC, and connecting the SCOPE base station to it, the sample xApp provided as part of ColO-RAN can be initialized. This can be done through the `setup-sample-xapp.sh` script by typing the followint commands on the ColO-RAN node. This script takes as input the ID of the RAN node the xApp subscribes to (e.g., the base station), which can be read in the logs of the ColO-RAN `e2term` Docker container, as described in the previous sections. It then builds the Docker image of the sample xApp, and starts it as a Docker container on the near-RT RIC.

{% highlight bash %}
cd setup-scripts/
./setup-sample-xapp.sh gnb:311-048-01000501
{% endhighlight %}

After the xApp container (named, for instance `sample-xapp`) has been started, the xApp process can be run with the following command. By running this command, the xApp subscribes to the RAN node specified in xApp build script above (through a RIC Subscription message), and triggers periodic reports (sent through RIC Indication messages) of RAN KPMs from the node.

{% highlight bash %}
docker exec -it sample-xapp /home/sample-xapp/run_xapp.sh
{% endhighlight %}

After performing these steps, the ColO-RAN sample xApp logs on file the KPMs received from the RAN node. Users of OpenRAN Gym can add custom intelligence (e.g., through AI/ML agents) to the xApp by modifying the template scripts in the `setup/sample-xapp/` directory, and rebuilding the xApp Docker image through the steps described in this section.

---

### References
> {{ publications.bonati2022openrangym }}
> {: .text-justify}

> {{ publications.bonati2022openrangym-pawr }}
> {: .text-justify}
