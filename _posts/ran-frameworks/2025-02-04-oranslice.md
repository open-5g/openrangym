---
layout: post
title: "ORANSlice: An Open-Source 5G Network Slicing Platform for O-RAN"
date: 2025-02-04
category: ran-frameworks
author:
short-description: "ORANSlice is an end-to-end open-source 5G network slicing framework for O-RAN based on OAI"
---

{% assign publications = site.data.publications %}

{% assign el = publications.publications | where: "name", "cheng2024oranslice" %}
{% assign element = el[0] %}
{% capture pub_cheng2024oranslice %}
{% include pub-template.html %}
{% endcapture %}

ORANSlice is an end-to-end open-source 5G network slicing framework for O-RAN. 
It (i) extends the 5G protocol stacks of OAI to support RAN slicing; (ii) implements an E2SM-CCC-based SM and xApp for RAN slicing control; (iii) conducts extensive testing and validation on [Arena](/experimental-platforms/arena) and <a href="https://x5g.org/" target="_blank">X5G</a> testbeds to ensure the robustness and effectiveness of the implementation.

The ORANSlice framework is shown in the diagram below.

<img src="/assets/post-assets/oranslice_framework.png" class="post-image" alt="ORANSlice Framework" width="75%">

For more details about this framework, visit the ORANSlice <a href="https://github.com/wineslab/ORANSlice" target="_blank">GitHub page</a>.

A tutorial on how to use ORANSlice is available [here](/tutorials/oranslice-tutorial).

If you use ORANSlice for your research, please reference the following paper:

> {{ pub_cheng2024oranslice | strip_newlines }}
> {: .text-justify}

This work was supported by the U.S. National Science Foundation under Grant CNS-2117814.
{: .text-justify}
