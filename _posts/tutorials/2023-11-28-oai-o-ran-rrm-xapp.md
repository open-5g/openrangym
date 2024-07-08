---
layout: post
title: "O-RAN xApps with an E2E 5G deployment: base example and GBR SLA"
date: 2023-11-28
category: tutorials
author:
short-description: How to setup an OAI-based O-RAN deployment with a basic monitoring xApp and an advanced xApp that manages GBR SLA
---

{% assign publications = site.data.publications %}

{% assign el = publications.publications | where: "name", "moro2023open" %}
{% assign element = el[0] %}
{% capture pub_moro2023open %}
{% include pub-template.html %}
{% endcapture %}

OpenAirInterface (OAI) is a 3GPP-compliant software implementation of the full 5G NR stack. In this tutorial, we show how OpenRAN Gym users can get started with deploying an OAI 5G NR SA network composed of a gNB and a few UEs on Colosseum. Additionally, this tutorial shows how to deploy a nRT-RIC and an xApp developed to manage Guaranteed Bit Rate (GBR) Service Level Agreements (SLAs) of the RAN UEs. 
{: .text-justify}
More details in this paper:
> {{ pub_moro2023open | strip_newlines }}
> {: .text-justify}
Please consider citing the paper above if you use the O-RAN-compliant OAI gNB, the xApp SDK and the custom E2SM definitions in your research. 

The basic repository for this [project can be found here](https://github.com/wineslab/OAI-colosseum-ric-integration). It contains submodules that point to the different projects that are relevant to this tutorial. 
{: .text-justify}

### Prerequisites
Please make a [Colosseum](/experimental-platforms/colosseum) reservation with the following images (credentials are `root`/`pass` for all):
- `oai-core-gbr` for the core network
- `oai-gbr-ran` for both the gNB and the UEs (at least 3 UEs are suggested)
- `nrt-ric-gbr` for the nRT-RIC and the xApp

### Deployment and testing
Once the reservation is ready, start the core network in the `oai-core` SRN:

{% highlight bash %}
./start_core.sh
{% endhighlight %}

Then start the near-RT RIC in the `oai-ric` SRN:

{% highlight bash %}
start-ric.sh
{% endhighlight %}

and take note of the IP address of the SRN:

{% highlight bash %}
ifconfig col0 | grep 'inet addr:' | cut -d: -f2 | awk '{ print $1}'
{% endhighlight %}

You can check if all the ric components are running by using the command `docker ps`, whose result should be this:

{% highlight bash %}
e2term:ricindi
e2mgr:latest
e2rtmansim:latest
dbaas:latest
xapp:latest
{% endhighlight %}

Start the gNB in the `oai-ran-gbr` SRN:

{% highlight bash %}
./run_gnb.sh -t donor
{% endhighlight %}

In the same SRN, start the `e2term`:

{% highlight bash %}
./run_e2sim.sh RIC_IP RIC_PORT
{% endhighlight %}

where `RIC_IP` is the `col0` IP address you have previously found, and `RIC_PORT` is the default `36422`. The E2 Agent of the gNB is successfully connected with the RIC if the following line is shown:

{% highlight bash %}
[E2AP] Received SETUP-RESPONSE-SUCCESS
{% endhighlight %}

Before connecting the reserved UEs to the gNB, start the following Colosseum RF scenario:

{% highlight bash %}
colosseumcli rf start -c 10011
{% endhighlight %}

Now connect all the UEs by running the following in the UE containers:

{% highlight bash %}
./run_ue.sh
{% endhighlight %}

We now start the xApp. Back to the RIC container, attach a terminal to the `xApp` docker container:

{% highlight bash %}
docker exec -it xapp bash
{% endhighlight %}

# A basic xApp and different SLA management xApp are available, please refer to the paper above for all the details. 

# The basic monitoring xApp can be started as follows:
# {% highlight bash %}
# python3 base_xapp.py
# {% endhighlight %}

# After stopping the basic xApp, we start the advanced xApp implementing an elastic SLA policy:

Different SLA management xApps are available, please refer to the paper above for all the details. We start the advanced xApp implementing an elastic SLA policy:
{% highlight bash %}
python3 elastic_sla.py
{% endhighlight %}

Finally, we can generate downlink UE traffic as follows. In every UE container, add the route to the core network host:

{% highlight bash %}
ip route add 192.168.70.128/26 via 12.1.1.1
{% endhighlight %} 

and take note of the UE GPRS Tunneling Protocol (GTP) endpoint IP:

{% highlight bash %}
ifconfig oaitun_ue1 | grep 'inet addr:' | cut -d: -f2 | awk '{ print $1}'
{% endhighlight %}

Run an `iperf3` server in the UE SRN:

{% highlight bash %}
iperf3 -s
{% endhighlight %}

and test the connection in downlink by running this in the `oai-core` SRN:

{% highlight bash %}
iperf3 -c UE_IP 
{% endhighlight %}

where UE_IP is the UE GTP endpoint IP.
