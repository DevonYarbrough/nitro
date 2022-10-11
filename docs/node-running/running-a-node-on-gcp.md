
# Running a Node on GCP

This tutorial explains how to configure and deploy a public node to connect to Arbitrum using Google Cloud Platform (GCP). Running a node in the cloud usually provides better stability and availability compared to running it on your laptop.

Note: If you’re interested in accessing the Arbitrum network but you don’t want to setup your own node, see our [Node Providers](./node-providers.md) to get RPC access to fully-managed nodes hosted by a third party provider

### Prerequisites

You can run the commands in this guide to deploy your full node on Google Compute Engine from any machine you want. From a [VM on GCP](https://cloud.google.com/compute), [Google Cloud Shell](https://cloud.google.com/shell), or your personal computer.

The following packages come pre-installed with Cloud Shell. Make sure to review the [documentation around ephermability](https://cloud.google.com/shell/docs/using-cloud-shell#choosing_ephemeral_mode) if you choose to use Cloud Shell. But if you are running the installation from your laptop or another machine, you need to install:
* Google Cloud cli: https://cloud.google.com/sdk/docs/install-sdk

Once you have installed the gcloud CLI, log into GCP using gcloud (https://cloud.google.com/sdk/gcloud/reference/auth/login)
```
$ gcloud auth login --update-adc
```

If you already have a GCP account setup, jump right into [Getting Started](#getting-started), if you don't, follow the sections below to create and configure your GCP account.

### GCP Setup

#### Sign Up for the 90 Day Free Trial
Google Cloud offers a [90 day $300 free trial for every new user](https://cloud.google.com/free/docs/gcp-free-tier/#free-trial). These $300 are given as credits to your account and you can use them to get a sense of Google Cloud products. Be aware that you will need to add payment information when signing up for the free trial. This is for identity verification purposes and [will not incur charges until you upgrade to a paid account and run out of credits](https://cloud.google.com/free/docs/gcp-free-tier/#:~:text=Don%27t%20worry%2C%20setting,90%2Dday%20period).). Some GCP feature such as GPUs and Windows servers are not available in the free trial. 

[Sign up for the $300 in credits here.](https://cloud.google.com/free)

#### Create a new GCP Project
You will also need to create a new project on the GCP Console or using the gcloud command from the Google Cloud CLI. Before you do that, however, it may be helpful to familiarize yourself with the [resource hierarchy on GCP](https://cloud.google.com/resource-manager/docs/cloud-platform-resource-hierarchy).

[Follow these instructions to setup a new project.](https://cloud.google.com/resource-manager/docs/creating-managing-projects#creating_a_project)

#### Enable billing / Upgrade your Account
You will still be able to use the free trial credits, but enabling billing allows you to have full access to all the features of GCP and not experience any interruption to your nodes.

[Upgrade your account by following the steps outlined here.](https://cloud.google.com/free/docs/gcp-free-tier#how-to-upgrade)

#### Further GCP Resources
This should be enough to get your GCP setup ready to start deploying your fullnod. But if you are brand new to GCP, you may want to check out some of our [quickstart guides](https://cloud.google.com/docs/get-started/quickstarts) and [Google Cloud Skills Boost](https://www.cloudskillsboost.google/catalog).

### Getting Started

Open the Cloud Shell or run the gcloud CLI command locally. You'll need to replace the project name, l1 url, and chain ID with the appropriate parameters from [Running a Node](./running-a-node.md):

```shell
gcloud compute instances create-with-container arbitrum-nitro-node \
--project=<PROJECT_NAME> \
--zone=us-central1-a \
--machine-type=n2-standard-8 \
--scopes=gke-default \
--tags=http-server,https-server \
--image=projects/cos-cloud/global/images/cos-stable-101-17162-40-5 \
--boot-disk-size=10GB \
--boot-disk-type=pd-balanced \
--boot-disk-device-name=arbitrum-nitro-node-boot-disk \
--container-image=offchainlabs/nitro-node:v2.0.7-10b845c \
--container-restart-policy=on-failure \
--container-arg=--l1.url=<L1_URL> \
--container-arg=--l2.chain-id=<CHAIN_ID> \
--container-arg=--http.api=net,web3,eth,debug \
--container-arg=--http.corsdomain=\* \
--container-arg=--http.addr=0.0.0.0 \
--container-arg=--http.vhosts=\*
```

Once your VM has been created, go to the Cloud Console and click "SSH" next to your VM.

It's also recommended that you include additional storage on your node.

```shell
--create-disk=boot=yes,device-name=arbitrum-nitro-node-boot-disk,image=projects/debian-cloud/global/images/debian-11-bullseye-v20220920,mode=rw,size=10,type=pd-balanced

--create-disk=device-name=disk-2,mode=rw,name=disk-2,size=1000,type=projects/gcda-dev/zones/us-central1-a/diskTypes/pd-ssd
```

### Monitoring

### Security
Firewall, Load balancer

### Updating Docker