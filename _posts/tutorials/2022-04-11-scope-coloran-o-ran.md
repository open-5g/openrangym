---
layout: post
title: "4G srsRAN with SCOPE and O-RAN near-RT RIC"
date: 2022-11-11
category: tutorials
author:
short-description: How to setup and use SCOPE and ColO-RAN near-RT RIC with Colosseum
---

{% assign publications = site.data.publications %}

{% assign el = publications.publications | where: "name", "bonati2022openrangym" %}
{% assign element = el[0] %}
{% capture pub_bonati2022openrangym %}
{% include pub-template.html %}
{% endcapture %}

{% assign el = publications.publications | where: "name", "bonati2023openrangympawr" %}
{% assign element = el[0] %}
{% capture pub_bonati2023openrangympawr %}
{% include pub-template.html %}
{% endcapture %}

{% assign el = publications.publications | where: "name", "polese2022coloran" %}
{% assign element = el[0] %}
{% capture pub_polese2022coloran %}
{% include pub-template.html %}
{% endcapture %}

In this tutorial, we show how to use the [SCOPE](/ran-frameworks/scope) and [ColO-RAN](/o-ran-frameworks/coloran) components of OpenRAN Gym to develop O-RAN-compliant Artificial Intelligence (AI)/Machine Learning (ML) solutions, deploy them as xApps on the near-RT RAN Intelligent Controller (RIC), and test them on large-scale softwarized Radio Access Network (RAN) deployed on the [Colosseum](/experimental-platforms/colosseum) wireless network emulator and controlled by the RIC.

#### Prerequisites
In the remaining of this tutorial, we assume users started an interactive reservation on Colosseum with:
- at least one SCOPE base station (`scope-with-e2` image, credentials are `root`/`scope`);
- at least one SCOPE User Equipment (UE) (`scope` image, credentials are `root`/`scope`);
- one ColO-RAN near-RT RIC node (`coloran-near-rt-ric` or `coloran-near-rt-ric-prebuilt` image, credentials are `root`/`ChangeMe` for both). **The RIC should be allocated to the last Standard Radio Node (SRN) in the Colosseum reservation.**

Procedures on how to use the OpenRAN Gym images on different testbeds (e.g., the [Arena](/experimental-platforms/arena) testbed, or to the POWDER and COSMOS testbeds of the [PAWR program](/experimental-platforms/pawr-platforms)) can be found at the end of this tutorial.

### Start Colosseum RF Scenario
Start a Radio Frequency (RF) scenario on Colosseum, e.g., scenario `1009`.

{% highlight bash %}
colosseumcli rf start 1009 -c
{% endhighlight %}


### Starting the SCOPE Base Station
As a first step, we start the SCOPE base station (the base station application will only start if this is the first node in the reservation, otherwise a UE application will start) and pass the configuration of the base station through the `radio_interactive.conf` file. In the SCOPE node, type the following commands. These commands take care of starting the base station and core network applications. They can also be used on other nodes to start the UEs applications (see below).

{% highlight bash %}
cd radio_api/
python3 scope_start.py --config-file radio_interactive.conf
{% endhighlight %}


### Starting the SCOPE UEs

You can use the same steps as before to start the SCOPE UEs. Run the following commands on all the terminals of the SRNs that you want to use as UEs.

{% highlight bash %}
cd radio_api/
python3 scope_start.py --config-file radio_interactive.conf
{% endhighlight %}


### Starting the ColO-RAN Near-RT RIC
Now we start the ColO-RAN near-RT RIC. In the ColO-RAN node, type the following commands. These commands will build and start the Docker images of the ColO-RAN near-RT RIC, and specify the network interface used by the RIC to communicate with the RAN (e.g., the `col0` interface in Colosseum).

{% highlight bash %}
cd /radio_code/colosseum-near-rt-ric/ # if needed
cd setup-scripts/
./setup-ric.sh col0
{% endhighlight %}

First, base Docker images, used to build the RIC images, are imported. Then, the four images composing the near-RT RIC are built, and their IP addresses and ports (defined in the `setup-lib.sh` script) are configured. 
These images include:

- `e2term`, which is the endpoint of the E2 messages on the RIC;
- `e2mgr`, which manages the messages to/from the E2 interface;
- `e2rtmansim`, which leverages the RIC Message Router (RMR) protocol to route the E2 messages inside the RIC;
- `db`, which maintains a database of the RAN nodes connected to the RIC.

After the building process completes, the Docker images are initialized as containers on a Colosseum SRN, and listen for incoming connections from RAN nodes implementing an E2 termination endpoint. 
The container logs can be read through the `docker logs` command, e.g., `docker logs e2term -f | grep gnb` shows the logs of the E2 termination (`e2term`) container.


### Connecting the SCOPE Base Station to ColO-RAN Near-RT RIC
After the ColO-RAN neat-RT RIC is started following the previous steps, the SCOPE base station can be connected to it. 
To this aim, the SCOPE base station runs an instance of the O-RAN E2 termination, which we adapted from the O-RAN Software Community (OSC) Distributed Unit (DU) implementation. 
After connecting to the near-RT RIC, this component can exchange messages with the xApps running therein. 
Specifically, the E2 termination of the base station can:

- receive RIC Subscription messages from the xApps;
- send periodic RAN Key Performance Measurements (KPMs) to the xApps through RIC Indication messages;
- receive RIC RAN Control (RC) messages from the xApps;
- interface with the SCOPE Application Programming Interfaces (APIs) to modify the scheduling and slicing configurations of the base station based on the received xApp control.

Edit the `build_odu.sh` script to include the IP address of the ColO-RAN node (e.g., the `col0` interface of the ColO-RAN node in Colosseum).

{% highlight bash %}
export RIC_HOST="<insert-ric-ip-address-here>"
export RIC_PORT=36422
export INTERFACE_TO_RIC="col0"
{% endhighlight %}

Initialize the E2 termination on the SCOPE base station by typing the following commands on the SCOPE node.

{% highlight bash %}
cd ~/radio_code/ # if needed
cd colosseum-scope-e2/
./build_odu.sh clean
./run_odu.sh
{% endhighlight %}

The E2 termination is first built through the `build_odu.sh` script, which also sets the IP address and port of the near-RT RIC to connect to, as well as the network interface used for the connection to the RIC.
Then, the E2 termination can be started through the `run_odu.sh` script, which initializes the E2 termination and connects it to the near-RT RIC.
The successful connection of base station and near-RT RIC can be verified by reading the logs of the `e2term` container on the ColO-RAN node, e.g.,:

{% highlight bash %}
docker logs e2term -f | grep gnb:
{% endhighlight %}

This log shows the association messages between the RIC and the base station, highlighting with the gNB ID of the connected base stations (e.g., `gnb:311-048-01000501`).
The logic to create the gNB ID is [here](https://github.com/wineslab/colosseum-scope-e2/blob/e97051d6879bd479ae8cf4e2ff095eea46a75855/src/du_app/du_e2ap_msg_hdl.c#L99-L138).


### Initializing a Sample xApp
After starting the near-RT RIC, and connecting the SCOPE base station to it, the sample xApp provided as part of ColO-RAN can be initialized. 
This can be done through the `setup-sample-xapp.sh` script by typing the followint commands on the ColO-RAN node. 
This script takes as input the ID of the RAN node the xApp subscribes to (e.g., the base station), which can be read in the logs of the ColO-RAN `e2term` Docker container, as described in the previous sections. 
It then builds the Docker image of the sample xApp, and starts it as a Docker container on the near-RT RIC.

{% highlight bash %}
cd setup-scripts/
./setup-sample-xapp.sh gnb:311-048-01000501
{% endhighlight %}

After the xApp container (named, for instance `sample-xapp-24`, which is the default name of the container created by the above script) has been started, the xApp process can be run with the following command. 
By running this command, the xApp subscribes to the RAN node specified in xApp build script above (through a RIC Subscription message), and triggers periodic reports (sent through RIC Indication messages) of RAN KPMs from the node.

{% highlight bash %}
docker exec -it sample-xapp-24 /home/sample-xapp/run_xapp.sh
{% endhighlight %}

After performing these steps, the ColO-RAN sample xApp logs on file the KPMs received from the RAN node. 
Users of OpenRAN Gym can add custom intelligence (e.g., through AI/ML agents) to the xApp by modifying the template scripts in the `setup/sample-xapp/` [directory](https://github.com/wineslab/colosseum-near-rt-ric/tree/main/setup/sample-xapp), and rebuilding the xApp Docker image through the steps described in this section.





### Use OpenRAN Gym on Different Testbeds
In this section, we illustrate how the OpenRAN Gym containerized applications, including the xApps developed and pre-trained on Colosseum, can be transferred to other testbeds (e.g., to the [Arena](/experimental-platforms/arena) testbed, or to the testbeds of the [PAWR program](/experimental-platforms/pawr-platforms)), and describe the necessary adjustments (if any) to run these applications on each experimental platform.

To facilitate these tasks, we developed some tools to automatically start the OpenRAN Gym LXC containers on the different platforms, and to properly interface them with the available radio resources. 
After the LXC images have been transferred (e.g., through the `scp` or `rsync` utilities) in a running instance of the testbed of interest, the image can be imported with the following command. 
This command imports the `scope-with-e2.tar.gz` LXC image transferred from Colosseum (i.e., the SCOPE image with the module for the E2 termination) to the compute machine of the remote testbed. 
After the above operation completes successfully, the new image, named `scope`, is visible by running the following command `lxc image list`.

{% highlight bash %}
lxc image import scope-with-e2.tar.gz --alias scope-e2
{% endhighlight %}

The LXC container can be, then, created from the imported image by running the command below. 
After creating the container, additional operations may be required based on the specific OpenRAN Gym image, and Software-defined Radio available in the remote testbed (e.g., USRP B210 or X310). 
The container may also require some additional permissions to be able to use the passed devices and interfaces (e.g., read/write permissions on the USB devices).

{% highlight bash %}
lxc init local:scope-e2 scope
{% endhighlight %}

In the case of ColO-RAN, which can be imported and started with commands analogous to the ones above, it is necessary to configure the Network Address Translation (NAT) rules of the host machine for it to forward the messages directed to the RIC to the ColO-RAN container (e.g., the E2 Setup Request message used by the base station to subscribe to the RIC, and the RIC Indication messages used to send the KPMs to the xApps. 
Similarly to the previous case, the LXC container may require some additional permissions (e.g., to run the Docker containers of the ColO-RAN near-RT RIC in a nested manner inside the ColO-RAN LXC container).

After these operations have been executed, the LXC container (e.g., the SCOPE LXC container created in the steps above) can be started with the following command.

{% highlight bash %}
lxc start scope
{% endhighlight %}

Now, the OpenRAN Gym applications can be executed by following the procedures detailed above.


#### Automated Scripts
To simplify and automate the above setup operations, and to allow OpenRAN Gym users to swiftly configure and run the transferred containers, we developed and open-sourced a set of scripts that take care of:

- passing the right radio interface to the containers
- giving them the required permissions
- setting up the NAT rules of the host machine
- starting the OpenRAN Gym LXC containers from the imported images

These scripts, which have been tested on the Arena, POWDER, and COSMOS testbeds, are available at the following [repository](https://github.com/wineslab/openrangym-pawr). 
They are supposed to be run after the OpenRAN Gym LXC images have been imported through the procedures above. 
The following command can be used to create, set up, and start the SCOPE LXC container starting from the imported LXC image.

{% highlight bash %}
./start-lxc-scope.sh testbed usrp_type [flash]
{% endhighlight %}

After creating the container on the testbed of interest (i.e., `arena`, `powder`, or `cosmos`), the script configures the USRP specified through the `usrp_type` parameter (i.e., `b210` or `x310`) following the procedures described above (i.e., passing to the container the devices to interface with the USRP, and assigning the appropriate permissions to the container). 
The optional `flash` parameter also allows to flash the FPGA of the USRP X310 with the UHD image used by the container. 
(Please note that after the FPGA has been flashed with a new image, the USRP may need to be rebooted. 
We refer to the documentation of the various testbeds for the instructions on how to achieve this.)

The following script can be used to create, setup, and start the ColO-RAN near-RT RIC container starting from the imported LXC image.

{% highlight bash %}
./start-lxc-ric.sh
{% endhighlight %}

This script creates the ColO-RAN near-RT RIC container, assigns it the required permissions (e.g., to run the nested Docker containers), and starts it. 
Then, it sets the NAT rules of the host machine (where the container is running) for it to forward the messages intended for the RIC. 
Finally, it builds and starts the Docker containers of the ColO-RAN near-RT RIC inside the created LXC container.

---

### References
> {{ pub_bonati2022openrangym | strip_newlines }}
> {: .text-justify}

> {{ pub_bonati2023openrangympawr | strip_newlines }}
> {: .text-justify}

> {{ pub_polese2022coloran | strip_newlines }}
> {: .text-justify}
