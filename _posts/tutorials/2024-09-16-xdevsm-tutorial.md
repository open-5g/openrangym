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

{% assign el = publications.publications | where: "name", "feraudo2026xdevsm" %}
{% assign element = el[0] %}
{% capture pub_feraudo2026xdevsm %}
{% include pub-template.html %}
{% endcapture %}

This guide provides an overview of deploying an xApp based on the xDevSM framework and offers examples of how to use it effectively. For detailed information about the framework's structure, refer to this [paper](#paper-reference).


## Prerequisites

Before you begin, ensure you have the following prerequisites installed and configured:

- Docker: no specific version;
- Helm: v3.11.2;
- OSC Near-RT RIC: Open-RAN Software Community (OSC) Near Real-Time Radio Intelligent Controller (RIC) **release-j**.

These tools are necessary for building and creating Helm charts for your xApp.

A complete guide on how to set-up a single-node k8s cluster to host the Near-RT RIC can be found [here.](https://github.com/aferaudo/ORANInABox/wiki/Prerequisite-Setup)

### Install Helm
Download helm binary

```bash
# The version we download is the one indicated by ORAN SC in their RIC installation guides
wget https://get.helm.sh/helm-v3.11.2-linux-amd64.tar.gz 
tar xvzpf helm-v3.11.2-linux-amd64.tar.gz
```

Install helm
```bash
sudo cp linux-amd64/helm /usr/local/bin/
```

Try it
```bash
helm version
```
### Deploying Near-RT RIC release J

The official guide is available [here](https://lf-o-ran-sc.atlassian.net/wiki/spaces/RICP/pages/14123019/2024-06-23+Release+J?preview=/14123019/14124761/command_list.txt).

> Note: The official guide contains an error: `./install -f ../RECIPE_EXAMPLE/example_recipe_oran_i_release.yaml` it should be `./install -f ../RECIPE_EXAMPLE/example_recipe_oran_j_release.yaml` instead.

Clone the repository:
```bash
git clone https://github.com/o-ran-sc/ric-plt-ric-dep.git
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

### Installing dms_cli tool for xApps onboarding and deployment
The ```dms_cli``` tool facilitates xApp onboarding and deployment. It uses the xApp descriptor and an additional schema file to generate xApp Helm charts.

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
>⚠️ WARNING: if you get this error: bash error: invalid command 'bdist_wheel' just run pip install wheel

> ⚠️ WARNING: if you get an error with fire package just run pip install --upgrade pip setuptools wheel importlib_metadata

> ❗ Note: Be sure to use the virtual environment .venv just created (. .venv/bin/activate) to call the dms_cli command

> ⚠️ WARNING: if you get this error ModuleNotFoundError: No module named 'pkg_resources' run pip install "setuptools<70"

## xApp example using xDevSM
**By default xDevSM supports `KPM V3.00` and `RC V1.03`**

Clone the examples repository and initialize xDevSM as a submodule:

```bash
git clone https://github.com/wineslab/xDevSM-xapps-examples.git
cd xDevSM-xapps-examples
git submodule init
git submodule update
```

xDevSM relies on the [decorator design pattern](https://refactoring.guru/design-patterns/decorator). So, we need to attach new behaviors to objects by placing these objects inside special wrapper objects that contain the behaviors. General object where we need to attach new behaviors in xDevSM is `xDevSMRMRXapp`.

### Create your own xApp
The directory structure for each xApp follows this convention:
```
<xapp_name>/
│── <xapp_name>.py
│── setup_imports.py
│── requirements.txt
├── config/
│   ├── config-file.json
│   └── schema.json
docker/
└── Dockerfile.<xapp_name>
```

#### KPM Example

Creating a generic xDevSM RMR xApp:

```python
xapp_gen = xDevSMRMRXapp("0.0.0.0", route_file=args.route_file)


# Adding kpm APIs to the xapp
kpm_xapp = XappKpmFrame(xapp_gen, 
                        xapp_gen.logger, 
                        xapp_gen.server, 
                        xapp_gen.get_xapp_name(), 
                        xapp_gen.rmr_port, 
                        xapp_gen.http_port,xapp_gen.get_pltnamespace(), 
                        xapp_gen.get_app_namespace())

# Registering the outermost rmr handler
xapp_gen.register_handler(kpm_xapp.handle)

# Getting RAN Functions decoded
ran_function_description = kpm_xapp.get_ran_function_description(json_ran_info=gnb_info)

# Subscribe to an E2 Node
kpm_xapp.subscribe(gnb=gnb, ev_trigger=ev_trigger_tuple, func_def=func_def_dict, ran_period_ms=1000, sst=args.sst, sd=args.sd)
```

Take a look at [this xApp](https://github.com/wineslab/xDevSM-xapps-examples/tree/main/kpm_prb_xapp) to see how you can use multiple service models functionalities.

Once created you need to build and deploy your xApp.

#### kpm-basic-xapp dockerfile
Docker file: `xDevSM-xapps-examples/docker/Dockerfile.kpm_basic_xapp.dev`.

For automatic running:
``` bash
#  No parameters used
CMD ["python", "kpm_xapp.py"]

# Influx 
CMD python3 kpm_xapp.py --influx_end_point http://<ip>:port -o <org> -t <token> -b <bucket> 

# You may use other parameters like gnb_target, ssd, and sd
```
> **Important note**: `ENV PLT_NAMESPACE="ricplt-j"` change this in the dockerfile based on your deployment

For manual running:
```bash
ENTRYPOINT ["sleep", "infinity"]
```

Then build the image as outlined [here](#build-the-xapp-kpm-basic-example).

### Build the xApp: KPM-basic example

> Note: A full table of the supported service model can be found [here](https://github.com/wineslab/xDevSM?tab=readme-ov-file#supported-service-model-actions)


In [this repository](https://github.com/wineslab/xDevSM-xapps-examples),  you can find an example of a complete KPM xApp.
This xApp can store KPM data in CSV files, InfluxDB and Redis. It subscribes to a single E2 node, which can be specified as an input parameter or defaults to the first available node to reply.

Clone the repository as previously mentioned.

> Note: The following steps are general, so you can also apply them to build the image of your custom xApp.

Build the Image of the xApp:
> Note: names and tag should match with those specified in the [config files](https://github.com/wineslab/xDevSM-xapps-examples/blob/main/kpm_basic_xapp/config/config-file.json)

```bash
docker build --tag kpm-basic-xapp:0.3.0-dev --file docker/Dockerfile.kpm_basic_xapp.dev .
```

> **Important Note:** Make sure the image name and tag match those specified in `config/config-file.json`. Also update the `APP_NAMESPACE` field in the config file to match your deployment's namespace (e.g., `ricxapp-j`).

Push the image to a registry (e.g., Docker Hub):
```bash
docker tag kpm-basic-xapp:0.3.0-dev <your_username>/kpm-basic-xapp:0.3.0-dev # use the versioning you want
docker push <your_username>/kpm-basic-xapp:0.3.0-dev # use the versioning you want
```

Change the xApp config file (**xapps-repo → kpm_basic_xapp → config**):
```javascript
 // config-file.json
 //...
    "name": "kpm-basic-xapp",
    "version": "0.3.0-dev",
    "APP_NAMESPACE": "ricxapp-j", 
    "containers": [
        {
            "name": "kpm-basic-xapp",
            "image": {
                "registry": "docker.io",
                "name": "angeloferaudo/kpm-basic-xapp",
                "tag": "0.3.0-dev"
            }
        }
    ],
```
> **Important Note:** change the `APP_NAMESPACE` with the one used in your deployment.



## Deploying an xApp using KPM basic example

To begin, ensure you have an instance of ```ChartMuseum``` running. You can do this either locally or through a Docker container:
```bash
docker run --rm -u 0 -it -d -p 8090:8080 \
  -e DEBUG=1 -e STORAGE=local -e STORAGE_LOCAL_ROOTDIR=/charts \
  -v $(pwd)/charts:/charts chartmuseum/chartmuseum:latest

export CHART_REPO_URL=http://0.0.0.0:8090
```
### Onboarding the xApp

Once ChartMuseum is running, you can onboard the xApp:
```bash
dms_cli onboard \
  --config-file-path <kpm-basic-xapp-path>/config/config-file.json \
  --shcema_file_path <kpm-basic-xapp-path>/config/schema.json
```
### Downloading the Helm Chart Package
To download the Helm chart for your xApp, use:
```bash
dms_cli download_helm_chart kpm-basic-xapp 0.3.0.dev # change name and version accordingly
```

> Note: You can also do this using `dms_cli install`. Please refer to [this tutorial](https://lf-o-ran-sc.atlassian.net/wiki/download/attachments/14123019/command_list.txt?version=1&modificationDate=1719164970168&cacheVersion=1&api=v2).

### Installing the xApp

Once the Helm chart is downloaded, install it into the xApp namespace:
```bash
helm install kpm-basic-xapp kpm-basic-xapp-0.3.0.dev.tgz -n ricxapp-j
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
# this downloads the flexric repository within the openairinterface5g project
# directory openair2/E2AP/flexric
git submodule init
git submodule update
```
> Note: to build the Service Models use `gcc-12`

Build and install the Service Models:
```bash
cd openair2/E2AP/flexric
mkdir build
cd build
cmake .. -DKPM_VERSION=KPM_V3_00
make -j ${nproc}
sudo make install
```

Take note of the directory where the service models are installed.

Build gnb with the `build-e2` option enabled:
```bash
# THIS IS JUST AN EXAMPLE, USE THE OPTION YOU NEED ONLY + --build-e2
 ./build_oai --build-e2 --ninja --gNB --nrUE --ninja --cmake-opt -DKPM_VERSION=KPM_V3_00
```

Enable the E2 connection by updating the gNB configuration file:
```javascript
e2_agent = {
  near_ric_ip_addr = "127.0.0.1"; // near-RT RIC address
  sm_dir = "/usr/local/lib/flexric/" // service model directory --> CHANGE THIS ACCORDINGLY 
}
```
> Note: by default the OAI E2 Agent uses a different port to connect to the near-RT RIC. This is hard-coded. You may have two solutions: 1. expose the e2term service on a different port (on the RIC); 2. change the [e2 agent code](https://gitlab.eurecom.fr/mosaic5g/flexric/-/blob/dev/src/agent/e2_agent_api.c?ref_type=heads#L94) and rebuild.


Run the gNB:
```bash
# Adjust options for your deployment (real/simulated)
sudo ./nr-softmodem -O <configuration_file> --gNBs.[0].min_rxtxtime 6 --rfsim --rfsimulator.serveraddr server
```

## Paper References

> {{ pub_feraudo2024xDevSM | strip_newlines }}
> {: .text-justify}

> {{ pub_feraudo2026xdevsm | strip_newlines }}
> {: .text-justify}
