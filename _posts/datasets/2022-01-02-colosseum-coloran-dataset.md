---
layout: post
title: "Colosseum ColO-RAN Dataset"
date: 2022-01-03
category: datasets
author:
short-description:
---

{% assign publications = site.data.publications %}

This is the dataset for the paper M. Polese, L. Bonati, S. D'Oro, S. Basagni, T. Melodia, <i>"ColoRAN: Design and Testing of Open RAN Intelligence on Large-scale Experimental Platforms,"</i> IEEE Transactions on Mobile Computing, pp. 1–14, July 2022.
The dataset can be found <a href="https://github.com/wineslab/colosseum-oran-commag-dataset" target="_blank">at this link</a>.
{: .text-justify}

We perform data-driven closed-loop control implemented using the [ColO-RAN](/o-ran-frameworks/coloran) RIC and the [SCOPE](/ran-frameworks/scope) framework on the [Colosseum](/experimental-platforms/colosseum) wireless network emulator. We demonstrate the feasibility of a closed-control loop where DRL agents running in xApps on the near-real-time RIC select the best-performing scheduling policy for each RAN slice.
{: .text-justify}

<img src="/assets/post-assets/coloran-architecture.png" class="post-image" alt="ColO-RAN Architecture" width="75%">

We emulate a 5G network with 7 base stations and 42 users in the dense urban scenario of Rome, Italy. The locations of the base stations have been extracted from OpenCelliD (a database of real-world cellular deployments) and cover an area of 0.11 km<sup>2</sup>. We consider a multi-slice scenario in which the users are statically assigned to a slice of the network and request three different traffic types: high-capacity enhanced mobile broadband (eMBB), ultra-reliable low-latency communications (URLLC), and machine-type communications (MTC). This reflects the case, for instance, of telecom operators providing different levels of service to different devices (e.g., MTC service to IoT-enabled devices, or URLLC to devices for time-critical applications). The base stations serve each slice with a dedicated (and possibly different) scheduling policy, selecting among proportionally fair (PF), waterfilling (WF), and round-robin (RR). We also consider the case where the number of physical resource blocks (PRBs) allocated to each slice varies over time.
{: .text-justify}

The dataset can be found <a href="https://github.com/wineslab/colosseum-oran-coloran-dataset" target="_blank">at this link</a>.
{: .text-justify}

If you use this dataset, please reference the following paper:

> {{ publications.polese2022coloran }}
> {: .text-justify}
