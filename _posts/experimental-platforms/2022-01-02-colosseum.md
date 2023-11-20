---
layout: post
title: "Colosseum"
date: 2022-01-02
category: experimental-platforms
author:
short-description: Colosseum Wireless Network Emulator
---

{% assign publications = site.data.publications %}

{% assign el = publications.other | where: "name", "bonati2021colosseum" %}
{% assign element = el[0] %}
{% capture pub_bonati2021colosseum %}
{% include pub-template.html %}
{% endcapture %}

Colosseum is the world’s largest wireless network emulator designed to support research and development of large-scale, next generation radio network technologies in a repeatable and highly configurable Radio-frequency (RF) and traffic environments.
{: .text-justify}

<div class="row">
    <div class="column" style="float: left; width: 50%; padding: 5px;">
        <img src="/assets/post-assets/colosseum-room-1.jpg" alt="Colosseum Datacenter" style="width:100%">
    </div>
    <div class="column" style="float: left; width: 50%; padding: 5px;">
        <img src="/assets/post-assets/colosseum-room-2.jpg" alt="Colosseum Datacenter Side" style="width:100%">
    </div>
</div> 

&nbsp;

It combines 128 Standard Radio Nodes (SRNs), each equipped with NI USRP X310 Software-defined Radios (SDRs), with a Massive digital Channel Emulator (MCHEM) backed by an extensive FPGA routing fabric. MCHEM emulates in FPGA real-world wireless RF channels between the SRNs and it is able to capture effects such as fading, multipath, etc., for up to 256x256 independently customizable channels. Accessible as a cloud-based platform, Colosseum also provides unique experimentation and data-collection capabilities.
{: .text-justify}

<img src="/assets/post-assets/colosseum-architecture.png" class="post-image" alt="Colosseum Architecture" width="90%">

As an RF testbed Colosseum can be utilized to:
- Emulate multiple operational environments including in real-time with effects such as multipath and fading effects with high-fidelity ray-tracing
- Instantiate softwarized protocol stacks on a general-purpose infrastructure
- Carry out large scale testing and data-collection
- Implement Machine Learning algorithms in different wireless communications techniques such as spectrum sharing, dynamic spectrum access, extraction of signal intelligence and optimized routing, by providing built-in powerful computational resources

If you use Colosseum for your research, please reference the following paper:

> {{ pub_bonati2021colosseum | strip_newlines }}
> {: .text-justify}

Visit the <a href="https://www.northeastern.edu/colosseum" target="_blank">Colosseum website</a> for more information about this platform.
{: .text-justify}

