---
layout: post
title: "Open RAN Commercial Traffic Twinning Dataset"
date: 2024-09-30
category: datasets
author:
short-description:
---

{% assign publications = site.data.publications %}

{% assign el = publications.publications | where: "name", "bonati2024twinning" %}
{% assign element = el[0] %}
{% capture pub_bonati2024twinning %}
{% include pub-template.html %}
{% endcapture %}

This is the dataset for the paper L. Bonati, R. Shirkhani, C. Fiandrino, S. Maxenti, S. D'Oro, M. Polese, T. Melodia, <i>"Twinning Commercial Network Traces on Experimental Open RAN Platforms,"</i> ACM WiNTECH, Washington, DC, USA, November 2024.
The dataset can be found <a href="https://github.com/wineslab/open-ran-commercial-traffic-twinning-dataset" target="_blank">at this link</a>.
{: .text-justify}


We design a methodology to twin real-world cellular traffics traces in experimental Open RAN testbeds. We demonstrate our approach on the [Colosseum](/experimental-platforms/colosseum) wireless network emulator. The real-world traffic traces we use is the public dataset of LTE network obtained with FALCON from multiple BSs located in different areas of Madrid, Spain which can be found <a href="https://git2.networks.imdea.org/wng/madrid-lte-dataset" target="_blank">at this link</a>. We instantiate an Open RAN deployment on Colosseum and perform an extensive data-collection compaign to collect a dataset of timestamped PHY-, MAC-, and APP-layer KMPs. We use <a href="https://github.com/USNavalResearchLaboratory/mgen" target="_blank">MGEN</a> to generate the twinned traffic traces and apply five different slicing configutaions, and two different schedualing policies.

<img src="/assets/post-assets/traffic-twinning-pipeline.png" class="post-image" alt="ColO-RAN Architecture" width="50%">

We emulate a 5G network with a base station and 8 User Equipments (UEs) in the dense urban scenario of Rome, Italy. We consider a multi-slice scenario in which the users are statically assigned to a slice of the network. We use MGEN to generate the downlink traffic for the UEs, which are assigned to different classes. UEs 1-4 request enhanced Mobile Broadband (eMBB) traffic and are allocated to slice 0, while UEs 5-8 request Ultra-reliable Low-latency Communications (URLLC) traffic and are allocated to slice 1. The UE-slice allocation is summarized in the following table.
{: .text-justify}

| UE ID | UE IMSI<sup>[1](#myfootnote1)</sup> | Traffic Type |
| ----- | ----------------------------------- | ------------ |
| 1     | 001010123456002                     | eMBB         |
| 2     | 001010123456003                     | eMBB         |
| 3     | 001010123456004                     | eMBB         |
| 4     | 001010123456005                     | eMBB         |
| 5     | 001010123456006                     | URLLC        |
| 6     | 001010123456007                     | URLLC        |
| 7     | 001010123456008                     | URLLC        |
| 8     | 001010123456009                     | URLLC        |

&nbsp;

Traffic is twinned from real-world traces from base stations in a suburban area of Madrid, covering three different spectrum bands (816 MHz, 1835 MHz, and 2650 MHz). For the same traffic and slicing configuration, we experiment with the proportionally fair and round-robin scheduling policies. The dataset contains five different slicing configurations, each showing how the 50 PRBs (10 MHz of spectrum) are allocated between the two slices. Details on the slice resource allocations in each of the considered slicing configurations are summarized in the following table.
{: .text-justify}

| Slice Resource Allocation | eMBB Slice PRBs | URLLC Slice PRBs |
| ------------------------- | --------------- | ---------------- |
| slicing 1                 | 9               | 41               |
| slicing 2                 | 21              | 29               |
| slicing 3                 | 30              | 20               |
| slicing 4                 | 39              | 11               |
| slicing 5                 | 50              | 0                |

&nbsp;

The dataset can be found <a href="https://github.com/wineslab/open-ran-commercial-traffic-twinning-dataset" target="_blank">at this link</a>.
{: .text-justify}

If you use this dataset, please reference the following paper:

> {{ pub_bonati2024twinning | strip_newlines }}
> {: .text-justify}

<a name="myfootnote1">1</a>: The UE IMSI is listed without the two leading zeros in the CSV files reported by the base station.
