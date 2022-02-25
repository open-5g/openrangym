---
layout: post
title: "Colosseum"
date: 2022-01-02
category: experimental-platforms
author:
short-description: Colosseum Wireless Network Emulator
---

{% assign publications = site.data.publications %}

Colosseum is the world’s largest wireless network emulator designed to support research and development of large-scale, next generation radio network technologies in a repeatable and highly configurable Radio-frequency (RF) and traffic environments. It combines 128 Standard Radio Nodes (SRNs) with a Massive digital Channel Emulator (MCHEM) backed by an extensive FPGA routing fabric. Each of the SRNs provides a platform for Software-defined Radio (SDR) and machine learning applications with two key hardware components, namely, a Dell R730 Server which comes with an NVIDIA K40M GPU, and an Ettus Research USRP X310 SDR that is equipped with a Xilinx Kintex 7 FPGA. The MCHEM facilitates real-world wireless RF channel emulation between the SRNs and can emulate fading, multipath, etc., for up to 256x256 independently customizable channels. This allows for large scale RF testing with up to 256 independent radio nodes each with powerful computational capabilities. Accessible as a cloud-based platform, Colosseum also provides other resources to create a real-time high-fidelity radio scenario such as traffic generation, timing, and GPS synthesis.
{: .text-justify}

<img src="/assets/post-assets/colosseum-architecture.png" class="post-image" alt="Colosseum Architecture" width="90%">

As an RF testbed Colosseum can be utilized to:
- Emulate multiple operational environments including a 1 sq. km open field, a dense urban city, a suburban shopping mall, a desert, or anything in-between.
- Emulate in real-time multipath and fading effects with high-fidelity ray-tracing.
- Support high-fidelity and large-scale research on waveforms, protocols at all layers, networked applications, jamming and security, MIMO, and beamforming.
- Provide full-stack repeatable environment (from RF to application layer).
- Carry out large scale testing, up to 256 radio nodes with 256x256 configurable channels.
- Support cellular networks (4G, 5G), IoT, cognitive radio, ad hoc networks, edge computing, and cloud RAN research.
- Implement Machine Learning algorithms in different wireless communications techniques such as spectrum sharing, dynamic spectrum access, extraction of signal intelligence and optimized routing, by providing built-in powerful computational resources.

Colosseum is remotely accessible to the users and operates 24/7/365. Users can reserve Colosseum resources through a simple web interface. Emulation jobs can be done either manually in an interactive session during the scheduled time, or the user may choose to create batch jobs that run automatically per user's instructions.
{: .text-justify}

Colosseum provides the users with preconfigured and ready to use LXC containers for basic testing. Users can customize these containers based on their needs to develop and implement their own radio codes. These customized container images can be uploaded to the Colosseum servers, and the user may choose to load the default or the customized containers onto the SRN's during their reserved session. This allows for completely customizable RF testing with a variety of repeatable scenarios that enables the users to conduct virtually any type of research on waveforms and different layers of protocol stack as well as networked applications, among others.
{: .text-justify}

If you use Colosseum for your research, please reference the following paper:

> {{ publications.bonati2021colosseum }}
> {: .text-justify}

Visit the <a href="https://www.northeastern.edu/colosseum" target="_blank">Colosseum website</a> for more information about this platform.
{: .text-justify}

