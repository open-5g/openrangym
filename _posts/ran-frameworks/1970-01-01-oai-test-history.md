---
layout: post
title: "OpenAirInterface Automated Testing History on Colosseum"
date: 1970-01-01
category: ran-frameworks
author:
short-description: Automated testing history of OAI on Colosseum
---

This page shows results from OpenAirInterface (OAI) iPerf3 tests run via [Colosseum](/experimental-platforms/colosseum) batch jobs. Tests are triggered automatically through Continuous Integration (CI) pipelines, such as <a href="https://jenkins-oai.eurecom.fr/job/RAN-Trigger-NEU-CI/" target="_blank">this</a>, which is hosted on the OAI Jenkins instance. Individual test results are analyzed through <a href="https://github.com/ztouchnetworks/openairinterface-automated-test-reports" target="_blank">this</a> automated report generation tool and stored on the above pipeline. Results of successful tests are saved in history files and used to compare new analyzed tests. A tabular version of the test history files is shown below. This page is automatically updated as new tests are run.

#### iPerf3  TCP Downlink

| Date                | Protocol   | Transmit Rate           |   Data Transferred [Mbit] |   Throughput [Mbps] |   Duration [s] | gNB Commit                                                                     | UE Commit                                                                      |
|---------------------|------------|-------------------------|---------------------------|---------------------|----------------|--------------------------------------------------------------------------------|--------------------------------------------------------------------------------|
| 2024-10-28 06:18:43 | TCP        | Transmit rate unlimited |                   4344.77 |             72.4126 |        60.0002 | [2c07f0081](https://gitlab.eurecom.fr/oai/openairinterface5g/commit/2c07f0081) | [2c07f0081](https://gitlab.eurecom.fr/oai/openairinterface5g/commit/2c07f0081) |
| 2024-10-26 07:53:43 | TCP        | Transmit rate unlimited |                   4364.47 |             72.7411 |        60.0001 | [2c07f0081](https://gitlab.eurecom.fr/oai/openairinterface5g/commit/2c07f0081) | [2c07f0081](https://gitlab.eurecom.fr/oai/openairinterface5g/commit/2c07f0081) |
| 2024-10-26 06:16:43 | TCP        | Transmit rate unlimited |                   4342.23 |             72.3704 |        60.0001 | [2c07f0081](https://gitlab.eurecom.fr/oai/openairinterface5g/commit/2c07f0081) | [2c07f0081](https://gitlab.eurecom.fr/oai/openairinterface5g/commit/2c07f0081) |
| 2024-10-25 21:54:46 | TCP        | Transmit rate unlimited |                   4209.41 |             70.157  |        60.0001 | [2c07f0081](https://gitlab.eurecom.fr/oai/openairinterface5g/commit/2c07f0081) | [2c07f0081](https://gitlab.eurecom.fr/oai/openairinterface5g/commit/2c07f0081) |
| 2024-10-25 07:52:43 | TCP        | Transmit rate unlimited |                   4344.87 |             72.4145 |        60.0001 | [2c07f0081](https://gitlab.eurecom.fr/oai/openairinterface5g/commit/2c07f0081) | [2c07f0081](https://gitlab.eurecom.fr/oai/openairinterface5g/commit/2c07f0081) |
| 2024-10-25 06:17:43 | TCP        | Transmit rate unlimited |                   4118.51 |             68.6419 |        60.0001 | [2c07f0081](https://gitlab.eurecom.fr/oai/openairinterface5g/commit/2c07f0081) | [2c07f0081](https://gitlab.eurecom.fr/oai/openairinterface5g/commit/2c07f0081) |
| 2024-10-24 07:52:43 | TCP        | Transmit rate unlimited |                   4012.38 |             66.8731 |        60.0001 | [2c07f0081](https://gitlab.eurecom.fr/oai/openairinterface5g/commit/2c07f0081) | [2c07f0081](https://gitlab.eurecom.fr/oai/openairinterface5g/commit/2c07f0081) |
| 2024-10-24 06:17:43 | TCP        | Transmit rate unlimited |                   4375.61 |             72.9263 |        60.0004 | [2c07f0081](https://gitlab.eurecom.fr/oai/openairinterface5g/commit/2c07f0081) | [2c07f0081](https://gitlab.eurecom.fr/oai/openairinterface5g/commit/2c07f0081) |

&nbsp;

#### iPerf3  TCP Uplink

| Date                | Protocol   | Transmit Rate           |   Data Transferred [Mbit] |   Throughput [Mbps] |   TCP Congestion Window [MB] |   Round-trip Time [ms] |   Duration [s] | gNB Commit                                                                     | UE Commit                                                                      |
|---------------------|------------|-------------------------|---------------------------|---------------------|------------------------------|------------------------|----------------|--------------------------------------------------------------------------------|--------------------------------------------------------------------------------|
| 2024-10-28 06:19:44 | TCP        | Transmit rate unlimited |                   1584.57 |             26.4098 |                      2.93971 |                717.712 |        60.0002 | [2c07f0081](https://gitlab.eurecom.fr/oai/openairinterface5g/commit/2c07f0081) | [2c07f0081](https://gitlab.eurecom.fr/oai/openairinterface5g/commit/2c07f0081) |
| 2024-10-26 07:54:44 | TCP        | Transmit rate unlimited |                   1597.04 |             26.6176 |                      2.91972 |                718.85  |        60.0001 | [2c07f0081](https://gitlab.eurecom.fr/oai/openairinterface5g/commit/2c07f0081) | [2c07f0081](https://gitlab.eurecom.fr/oai/openairinterface5g/commit/2c07f0081) |
| 2024-10-26 06:17:43 | TCP        | Transmit rate unlimited |                   1602.78 |             26.7127 |                      2.99154 |                725.009 |        60.0001 | [2c07f0081](https://gitlab.eurecom.fr/oai/openairinterface5g/commit/2c07f0081) | [2c07f0081](https://gitlab.eurecom.fr/oai/openairinterface5g/commit/2c07f0081) |
| 2024-10-25 21:55:46 | TCP        | Transmit rate unlimited |                   1579.19 |             26.3205 |                      2.99806 |                736.406 |        60.0001 | [2c07f0081](https://gitlab.eurecom.fr/oai/openairinterface5g/commit/2c07f0081) | [2c07f0081](https://gitlab.eurecom.fr/oai/openairinterface5g/commit/2c07f0081) |
| 2024-10-25 07:53:44 | TCP        | Transmit rate unlimited |                   1572.42 |             26.2072 |                      3.01218 |                727.783 |        60.0004 | [2c07f0081](https://gitlab.eurecom.fr/oai/openairinterface5g/commit/2c07f0081) | [2c07f0081](https://gitlab.eurecom.fr/oai/openairinterface5g/commit/2c07f0081) |
| 2024-10-25 06:18:43 | TCP        | Transmit rate unlimited |                   1607.3  |             26.7888 |                      2.8966  |                703.515 |        60.0002 | [2c07f0081](https://gitlab.eurecom.fr/oai/openairinterface5g/commit/2c07f0081) | [2c07f0081](https://gitlab.eurecom.fr/oai/openairinterface5g/commit/2c07f0081) |
| 2024-10-24 07:53:43 | TCP        | Transmit rate unlimited |                   1481.2  |             24.6873 |                      2.91048 |                763.669 |        60.0004 | [2c07f0081](https://gitlab.eurecom.fr/oai/openairinterface5g/commit/2c07f0081) | [2c07f0081](https://gitlab.eurecom.fr/oai/openairinterface5g/commit/2c07f0081) |
| 2024-10-24 06:18:43 | TCP        | Transmit rate unlimited |                   1573.53 |             26.226  |                      2.90739 |                730.217 |        60.0003 | [2c07f0081](https://gitlab.eurecom.fr/oai/openairinterface5g/commit/2c07f0081) | [2c07f0081](https://gitlab.eurecom.fr/oai/openairinterface5g/commit/2c07f0081) |

&nbsp;

Last updated: 2024-10-28 02:33:20 EDT
{: .text-right}
