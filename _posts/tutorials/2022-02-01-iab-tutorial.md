---
layout: post
title: "Tutorial on O-RAN and IAB on Colosseum"
date: 2022-02-01
category: tutorials
author:
short-description: How to setup and use an IAB network controlled by O-RAN in Colosseum
---

In this tutorial, we show how to use OpenAirInterface (OAI) with a [custom IAB implementation](https://dl.acm.org/doi/10.1145/3495243.3558750) to set up a working IAB 5G network deployed on the [Colosseum](/experimental-platforms/colosseum) wireless network emulator and controlled by the RIC. The network will be made of 10 SRN, divided into 1 IAB-donor, 4 IAB-nodes (each one made of one softUE and one gNB), and 1 softUE connected to the last IAB-node. In this way, it will be possible to assess the performances of a linear IAB network with 5 hops.

<!-- Later on, we'll describe how to deploy a realistic IAB network using the scenarios we developed using real data from different European cities. -->

## IAB RAN

#### Prerequisites

In the remaining of this tutorial, we assume users started an interactive reservation on Colosseum with at least 4 IAB RAN nodes (`oai-iab-ran`), one OAI IAB-aware core network (`oai-core-iab`) to deploy the network. For both images the login is `root` and the password is `pass`. The IAB network is then controlled by [iab-manager](https://github.com/wineslab/iab-manager), that can be run either locally or in the core network node.

### Linear IAB Network

Once the reservation is ready, iab-manager can be used to interact with the different components. First of all, if you haven't already downloaded it, clone the iab-manager repo on your local machine:

{% highlight bash %}
git clone https://github.com/wineslab/iab-manager.git
{% endhighlight %}

Then enter the directory and create a `.env` in the main folder containing your Colosseum credentials in the format:

{% highlight python %}
COLOSSEUM_USER = [username]
COLOSSEUM_PWD = [pwd]
{% endhighlight %}

This will allow iab-manager to fetch the reservations' data from the Colosseum API and automatically map the SRNs to IAB-nodes of your network.

Then, you must configure iab-manager through the `config.yml` configuration file. Create the file with the following content:

{% highlight yaml %}
topology: "toy1.graphml"
scenario: 20055
scenario_nodes: 25
srn_blacklist: []
results_folder: "./results"
if_freq: 0
{% endhighlight %}

You can then run iab-manager:
{% highlight bash %}
python3 iab-manager.py
{% endhighlight %}

The software will connect to the Colosseum reservation API to fetch the information about the current reservation, then it will read the content of the topology from `toy1.graphml`, which represents the linear topology we want to deploy and finally it will map each SRN from the reservation to a node from the topology, which will then be mapped to a radio in the scenario. At the end of the setup, iab-manager will show a prompt that allows the user to interact with the running network.

First, let's start the core network by typing `core start`.

Then, we can start the rf scenario by typing `rf_scenario start`.

Finally, we automatically start the network by issuing the command `autostart`.

It is also possible to start the network manually by starting first the iab-donor, then the iab-relays and finally the UE: `donor 0 start`, `iab_node start 12`, `iab_node start 34`, `iab_node start 56`, `iab_node start 78`, `ue 9 start`.

The numbers in the commands above identify the id of the node, which is set in the topology file (`toy1.graphml`). In the case of IAB-nodes, since two SRNs are combined to form a single IAB-node, the id is set in a separate property of the graph called `iab`. In this example is set to the union of two single ids, so if an IAB-node is made of an MT with id `1` and a DU with id `2`, its id will be `12`.

<!-- ### Realistic IAB Network -->

## RIC

To be updated.

### Near-RT RIC setup

To be updated.

### Non-RT RIC Setup

To be updated.
