---
layout: post
title: "Colosseum O-RAN COMMAG Dataset"
date: 2022-01-02
category: datasets
author:
short-description:
---

{% assign publications = site.data.publications %}

This is the dataset for the paper L. Bonati, S. D'Oro, M. Polese, S. Basagni, T. Melodia, <i>"Intelligence and Learning in O-RAN for Data-driven NextG Cellular Networks,"</i> IEEE Communications Magazine, vol. 59, no. 10, pp. 21–27, October 2021.
The dataset can be found <a href="https://github.com/wineslab/colosseum-oran-commag-dataset" target="_blank">at this link</a>.
{: .text-justify}

We perform data-driven closed-loop control implemented using the [ColO-RAN](/o-ran-frameworks/coloran) RIC and the [SCOPE](/ran-frameworks/scope) framework on the [Colosseum](/experimental-platforms/colosseum) wireless network emulator. We demonstrate the feasibility of a closed-control loop where DRL agents running in xApps on the near-real-time RIC select the best-performing scheduling policy for each RAN slice.
{: .text-justify}

<img src="/assets/post-assets/colosseum-o-ran-commag-architecture.png" class="post-image" alt="SCOPE Experiment Workflow" width="75%">

We emulate a 5G network with 4 base stations and 40 users in the dense urban scenario of Rome, Italy. The locations of the base stations have been extracted from OpenCelliD (a database of real-world cellular deployments) and cover an area of 0.11 km<sup>2</sup>. We consider a multi-slice scenario in which the users are statically assigned to a slice of the network and request three different traffic types: high-capacity enhanced mobile broadband (eMBB), ultra-reliable low-latency communications (URLLC), and machine-type communications (MTC). This reflects the case, for instance, of telecom operators providing different levels of service to different devices (e.g., MTC service to IoT-enabled devices, or URLLC to devices for time-critical applications). The base stations serve each slice with a dedicated (and possibly different) scheduling policy, selecting among proportionally fair (PF), waterfilling (WF), and round-robin (RR). We also consider the case where the number of physical resource blocks (PRBs) allocated to each slice varies over time.
{: .text-justify}

If you use this dataset, please reference the following paper:

> {{ publications.bonati2021intelligence }}
> {: .text-justify}
