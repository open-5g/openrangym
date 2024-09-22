---
layout: post
title: "Deploying an xDevSM-Based xApp with OSC NearRT-RIC"
date: 2024-09-16
category: tutorials
author: 
short-description: Guide for deploying an xApp with xDevSM and examples of the effective usage of the xDevSM framework.
---

This guide provides an overview of deploying an xApp based on the xDevSM framework and offers examples of how to use it effectively. For detailed information about the framework's structure, refer to the paper:

> Add paper


## Prerequisites

Before you begin, ensure you have the following prerequisites installed and configured:

- Docker;
- Helm;
- OSC Near-RT RIC:  Open-RAN Software Community (OSC) Near Real-Time Radio Intelligent Controller (RIC) **release-j**.

These tools are necessary for building and creating Helm charts for your xApp.

### Deploying Near-RT RIC release J

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

> Note: Details about why we are using ```flexric``` can be found in the paper.

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
```json
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



