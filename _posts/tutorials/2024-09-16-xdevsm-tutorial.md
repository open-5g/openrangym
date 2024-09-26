---
layout: post
title: "Deploying an xDevSM-Based xApp with the OSC NearRT-RIC"
date: 2024-09-16
category: tutorials
author: 
short-description: Guide for deploying an xApp with xDevSM and examples of the effective usage of the xDevSM framework.
---
{% assign publications = site.data.publications %}

{% assign el = publications.publications | where: "name", "feraudo2024xDevSM" %}
{% assign element = el[0] %}
{% capture pub_feraudo2024xDevSM %}
{% include pub-template.html %}
{% endcapture %}

This guide provides an overview of deploying an xApp based on the xDevSM framework and offers examples of how to use it effectively. For detailed information about the framework's structure, refer to this [paper](#paper-reference).


## Prerequisites

Before you begin, ensure you have the following prerequisites installed and configured:

- Docker: no specific version;
- Helm: v3.11.2;
- OSC Near-RT RIC: Open-RAN Software Community (OSC) Near Real-Time Radio Intelligent Controller (RIC) **release-j**.

These tools are necessary for building and creating Helm charts for your xApp.

### Deploying Near-RT RIC release J

The official guide is available [here](https://lf-o-ran-sc.atlassian.net/wiki/spaces/RICP/pages/14123019/2024-06-23+Release+J?preview=/14123019/14124761/command_list.txt).

> Note: The official guide contains an error: `./install -f ../RECIPE_EXAMPLE/example_recipe_oran_i_release.yaml` it should be `./install -f ../RECIPE_EXAMPLE/example_recipe_oran_j_release.yaml` instead.

Clone the repository:
```bash
https://github.com/o-ran-sc/ric-plt-ric-dep.git
cd ric-plt-ric-dep
git checkout j-release
```

Install chartmuseum and common charts:
```bash
# be sure to be in the 'bin' folder
cd bin
# This script will create a docker container running chartmuseum and will create a repo with common charts used to build the ric
sudo ./install_common_templates_to_helm.sh 
```

Deploy the near-RT RIC using the ```install``` script:
```bash
./install -f ../RECIPE_EXAMPLE/example_recipe_oran_j_release.yaml
```

Check if the Near-RT RIC is running:
```bash
kubectl get pods -n ricplt
```

If you're running Kubernetes on bare metal or virtualized hardware (not using Minikube), **be sure to expose the `e2term` service**.

```bash
kubectl -n ricplt expose deployment deployment-ricplt-e2term-alpha --protocol=SCTP --port=36422 --target-port=36422 --external-ip=<local-ip-nrric> --name=sctp-service
```

### Installing dms_cli tool
The ```dms_cli``` tool facilitates xApp onboarding. It uses the xApp descriptor and an additional schema file to generate xApp Helm charts.

```bash
git clone https://github.com/o-ran-sc/ric-plt-appmgr.git

# Switch to the branch corresponding to the Near-RT RIC
git checkout j-release

# go inside xapp onboarder
cd ric-plt-appmgr/xapp_orchestrater/dev/xapp_onboarder

# installing python 3.9 (needed for this repo)
sudo add-apt-repository ppa:deadsnakes/ppa
sudo apt update

# installing python and python venv
sudo apt install python3.9-dev python3.9-venv

# create virtual environment
python3.9 -m venv .venv

# activate the virtual environment
. .venv/bin/activate

# install requirements
pip install -r requirements.txt
pip install .
```

## xApp example using xDevSM

> Note: You can use the pre-built image provided in [xApp examples repository](https://github.com/wineslab/xDevSM-xapps-examples) (`angeloferaudo/kpm-basic-xapp`). This means that, after cloning the repository, **you can skip this section and proceed directly with the [xApp deployment](#deploying-an-xapp-using-kpm-basic-example)**. However, if you plan to make changes in the source code, you will need to build the image yourself.


### Create your own xApp (optional)

You can create your own xApp using xDevSM by following one of these strategies:

- Extending the ```XappKpmFrame```;
- Encapsulate an object of type ```XappKpmFrame``` and access to the public methods.

The [kpm example](https://github.com/wineslab/xDevSM-xapps-examples/tree/main/kpm_basic_xapp) uses the first strategy. In this case, you need to implement the ```logic()``` method where you can define your xApp's functionality.

#### xDevSM: configuring the service model

By default, xDevSM supports ```KPM V3.00```. However, it has also been tested with versions ```V2.01``` and ```V2.03```.

You can find the shared library (```libkpm_sm.so```) related to the service model in the following directory:

**xDevSM → sm_framework → lib**

##### Change Service Model version (optional)

If you wish to build a different version of the service model, clone the ```flexric``` repository:

```bash
git clone https://gitlab.eurecom.fr/mosaic5g/flexric.git
git checkout dev
```

Build ```flexric``` with your desired version of the service model (e.g., ```V2.03```):
```bash
cmake .. -DKPM_VERSION=KPM_V2_03
make
```

Locate the service model shared library and copy it into your xDevSM project:
```bash
find . -type f -name "*.so"
cp ./src/sm/kpm_sm/kpm_sm_v02.03/libkpm_sm.so xDevSM/sm_framework/lib 
```

> Note: Details about why we are using ```flexric``` can be found in the [paper](#paper-reference).

### Build the xApp: KPM-basic example

***By default xDevSM supports ```KPM V3.00```***


In [this repository](https://github.com/wineslab/xDevSM-xapps-examples),  you can find an example of a KPM xApp that prints data received by the gNB.

Clone the repository and initialize the submodules:
```bash
git clone https://github.com/wineslab/xDevSM-xapps-examples.git

# clone xDevSM code
git submodule init
git submodule update
```

> Note: The following steps are general, so you can also apply them to build the image of your custom xApp.

Build the Image of the xApp:
```bash
docker build --tag kpm-basic-xapp:0.1.0 --file docker/Dockerfile.kpm_basic_xapp .
```

Push the Image to a Repository:
```bash
docker tag kpm-basic-xapp:0.1.0 <your_username>/kpm-basic-xapp:0.1.0
docker push <your_username>/kpm-basic-xapp:0.1.0
```

Change the xApp config file (**xapps-repo → kpm_basic_xapp → config**):
```javascript
 // config-file.json
 //...
    "containers": [
        {
            "name": "kpm-basic-xapp",
            "image": {
                "registry": "docker.io",
                "name": "<your_username>/kpm-basic-xapp", // use username
                "tag": "0.1.0"
            }
        }
    ],
```


## Deploying an xApp using KPM basic example

To begin, ensure you have an instance of ```ChartMuseum``` running. You can do this either locally or through a Docker container:
```bash
docker run --rm -u 0 -it -d -p 8090:8080 \
  -e DEBUG=1 -e STORAGE=local -e STORAGE_LOCAL_ROOTDIR=/charts \
  -v $(pwd)/charts:/charts chartmuseum/chartmuseum:latest

export CHART_REPO_URL=http://0.0.0.0:8090
```
### Oboarding the xApp

Once ChartMuseum is running, you can onboard the xApp:
```bash
dms_cli onboard \
  --config-file-path <kpm-basic-xapp-path>/config/config.json \
  --shcema_file_path <kpm-basic-xapp-path>/config/schema.json
```
### Downloading the Helm Chart Package
To download the Helm chart for your xApp, use:
```bash
dms_cli download_helm_chart kpm-basic-xapp 0.1.0
```

> Note: You can also do this using `dms_cli install`. Please refer to [this tutorial](https://lf-o-ran-sc.atlassian.net/wiki/download/attachments/14123019/command_list.txt?version=1&modificationDate=1719164970168&cacheVersion=1&api=v2).


```bash
helm install kpm-basic-xapp kpm-basic-xapp-0.1.0.tgz -n ricxapp
```


## Additional Deployment Insights

### SRS RAN

The official tutorial on connecting SRS RAN to the OSC Near-RT RIC can be found [here](https://docs.srsran.com/projects/project/en/latest/tutorials/source/near-rt-ric/source/index.html).

Update the gNB configuration file as follows:
```yaml
e2:
  enable_du_e2: true                # Enable DU E2 agent (one for each DU instance)
  e2sm_kpm_enabled: true            # Enable KPM service module
  addr: <near-rt-ric-address>                   # RIC IP address
  #bind_addr: 127.0.0.100           # A local IP that the E2 agent binds to for traffic from the RIC. ONLY required if running the RIC on a separate machine. 
  port: 36422                       # RIC port
```

When running the gNB, bind it to the local address:
```bash
sudo ./gnb -c customConfigs/gnb_zmq.yaml e2 --addr=<near-rt-ric-address> --bind_addr=<local-address>
```

### OAI RAN

To connect OAI RAN to the Near-RT RIC, you need to build the gNB with the E2 agent enabled.

The official documentation is available [here](https://gitlab.eurecom.fr/oai/openairinterface5g/-/blob/develop/openair2/E2AP/README.md#212-build-oai-with-e2-agent).

After cloning the [repository](https://gitlab.eurecom.fr/oai/openairinterface5g) initialize the submodules:
```bash
git clone https://gitlab.eurecom.fr/oai/openairinterface5g.git
cd openairinterface5g
# this downlods the flexric repository within the openairinterface5g project
# directory openair2/E2AP/flexric
git submodule init
git submodule update
```

Build and install the Service Models:
```bash
cd openair2/E2AP/flexric
mkdir build
cd build
cmake ..
make
sudo make install
```

Take note of the directory where the service models are installed.

Build gnb with the `build-e2` option enabled:
```bash
# THIS IS JUST AN EXAMPLE, USE THE OPTION YOU NEED ONLY + --build-e2
 ./build_oai -c --ninja \
      --eNB --gNB --RU --UE --nrUE \
      --build-lib "telnetsrv enbscope uescope nrscope" \
      -w USRP -t Ethernet \
      --build-e2 --cmake-opt -DXAPP_MULTILANGUAGE=OFF \
      --noavx512 \
      --cmake-opt -DCMAKE_C_FLAGS="-Werror" --cmake-opt -DCMAKE_CXX_FLAGS="-Werror" $BUILD_OPTION
```

Enable the E2 connection by updating the gNB configuration file:
```javascript
e2_agent = {
  near_ric_ip_addr = "127.0.0.1";
  sm_dir = "/usr/local/lib/flexric/" // service model directory --> CHANGE THIS ACCORDINGLY 
}
```

Run the gNB:
```bash
# Adjust options for your deployment (real/simulated)
sudo ./nr-softmodem -O <configuration_file> --rfsim --sa -E
```

## Paper Reference

> {{ pub_feraudo2024xDevSM | strip_newlines }}
> {: .text-justify}
