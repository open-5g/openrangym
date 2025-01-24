---
layout: post
title: "Publications"
date: 2022-01-07
category: other
author:
short-description:
---

{% assign publications = site.data.publications %}

{% assign el = publications.publications | where: "name", "bonati2023openrangympawr" %}
{% assign element = el[0] %}
{% capture pub_bonati2023openrangympawr %}
{% include pub-template.html %}
{% endcapture %}

{% assign el = publications.publications | where: "name", "lacava2022programmable" %}
{% assign element = el[0] %}
{% capture pub_lacava2022programmable %}
{% include pub-template.html %}
{% endcapture %}

{% assign el = publications.publications | where: "name", "lacava2025dapps" %}
{% assign element = el[0] %}
{% capture pub_lacava2025dapps %}
{% include pub-template.html %}
{% endcapture %}

OpenRAN Gym is an open-source project fostering collaborative, AI-driven and experimental research in the Open RAN ecosystem.
The goal of OpenRAN Gym is to bring together researchers from academia and industry to create a vibrant, dynamic, evolving and cooperative ecosystem advancing research and development of cutting-edge and groundbreaking solutions for the Open RAN.
We welcome contributions from the community, please follow this link to [contribute](/other/contribute) to OpenRAN Gym.
{: .text-justify}

If you use the components part of OpenRAN Gym, please reference the following paper and the paper relative to the specific component that you are using.
{: .text-justify}

> {{ pub_bonati2023openrangympawr | strip_newlines }}
> {: .text-justify}

If you use [ns-O-RAN](/ran-frameworks/ns-o-ran), please reference the following paper:

> {{ pub_lacava2022programmable | strip_newlines }}
> {: .text-justify}

If you use [dApps](/ran-frameworks/dapps), please reference the following paper:

> {{ pub_lacava2025dapps | strip_newlines }}
> {: .text-justify}

&nbsp;

**Publications**

OpenRAN Gym features the following publications:
{: .text-justify}

{% for element in publications.publications reversed %}

{% capture pub_item %}
{% include pub-template.html %}
{% endcapture %}

> {{ pub_item | strip_newlines }}
> {: .text-justify}
{% if forloop.last %}{: reversed="reversed"}{% endif %}

{% endfor %}

&nbsp;

{% include support.md %}
