---
layout: post
title: "OpenAirInterface Automated Testing History on Colosseum"
date: 2024-10-24
category: automated-testing
author:
short-description: Automated testing history of OAI on Colosseum
---

This page shows results from OpenAirInterface (OAI) iPerf3 tests run via [Colosseum](/experimental-platforms/colosseum) batch jobs. Tests are triggered automatically through Continuous Integration (CI) pipelines, such as <a href="https://jenkins-oai.eurecom.fr/job/RAN-Trigger-NEU-CI/" target="_blank">this</a>, which is hosted on the OAI Jenkins instance. Individual test results are analyzed through <a href="https://github.com/ztouchnetworks/openairinterface-automated-test-reports" target="_blank">this</a> automated report generation tool and stored on the above pipeline. Results of successful tests are saved in history files and used to compare new analyzed tests. A tabular version of the test history files is shown below. This page is automatically updated as new tests are run.

#### iPerf3  TCP Downlink

| Date                | Protocol   | Transmit Rate           |   Data Transferred [Mbit] |   Throughput [Mbps] |   Duration [s] | gNB Commit                                                                     | UE Commit                                                                      |
|---------------------|------------|-------------------------|---------------------------|---------------------|----------------|--------------------------------------------------------------------------------|--------------------------------------------------------------------------------|
| 2024-10-24 07:52:43 | TCP        | Transmit rate unlimited |                   4012.38 |             66.8731 |        60.0001 | [2c07f0081](https://gitlab.eurecom.fr/oai/openairinterface5g/commit/2c07f0081) | [2c07f0081](https://gitlab.eurecom.fr/oai/openairinterface5g/commit/2c07f0081) |
| 2024-10-24 06:17:43 | TCP        | Transmit rate unlimited |                   4375.61 |             72.9263 |        60.0004 | [2c07f0081](https://gitlab.eurecom.fr/oai/openairinterface5g/commit/2c07f0081) | [2c07f0081](https://gitlab.eurecom.fr/oai/openairinterface5g/commit/2c07f0081) |


#### iPerf3  TCP Uplink

| Date                | Protocol   | Transmit Rate           |   Data Transferred [Mbit] |   Throughput [Mbps] |   TCP Congestion Window [MB] |   Round-trip Time [ms] |   Duration [s] | gNB Commit                                                                     | UE Commit                                                                      |
|---------------------|------------|-------------------------|---------------------------|---------------------|------------------------------|------------------------|----------------|--------------------------------------------------------------------------------|--------------------------------------------------------------------------------|
| 2024-10-24 07:53:43 | TCP        | Transmit rate unlimited |                   1481.2  |             24.6873 |                      2.91048 |                763.669 |        60.0004 | [2c07f0081](https://gitlab.eurecom.fr/oai/openairinterface5g/commit/2c07f0081) | [2c07f0081](https://gitlab.eurecom.fr/oai/openairinterface5g/commit/2c07f0081) |
| 2024-10-24 06:18:43 | TCP        | Transmit rate unlimited |                   1573.53 |             26.226  |                      2.90739 |                730.217 |        60.0003 | [2c07f0081](https://gitlab.eurecom.fr/oai/openairinterface5g/commit/2c07f0081) | [2c07f0081](https://gitlab.eurecom.fr/oai/openairinterface5g/commit/2c07f0081) |


