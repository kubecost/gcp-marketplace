# Overview

Kubecost helps you monitor and manage cost and capacity in Kubernetes environments. We integrate with your infrastructure to help your team track, manage, and reduce spend.

[Learn more.](https://www.kubecost.com) or check our official documentation at https://guide.kubecost.com/

# Installation

## Quick install with Google Cloud Marketplace

Get up and running with a few clicks!
Install Kubecost to a Google Kubernetes Engine cluster using Google Cloud Marketplace.
Follow the [on-screen instructions](https://console.cloud.google.com/marketplace/details/tbd).

## Command line instructions

### Prerequisites

#### Set up command-line tools

You'll need the following tools in your development environment:
- [gcloud](https://cloud.google.com/sdk/gcloud/)
- [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/)
- [helm](https://github.com/helm/helm)
- [docker](https://docs.docker.com/install/)

Configure `gcloud` as a Docker credential helper:

```bash
gcloud auth configure-docker
```
#### Create a Google Kubernetes Engine cluster

Create a new cluster from the command line:

```bash
# Example cluster name: kubecost-cluster
export CLUSTER=<YOUR_CLUSTER_NAME>
# Example cluster name: us-central1-a
export ZONE=<YOUR_ZONE>

gcloud container clusters create "$CLUSTER" --zone "$ZONE"
```

Configure `kubectl` to connect to the new cluster:

```bash
gcloud container clusters get-credentials "$CLUSTER" --zone "$ZONE"
```

#### Clone this repo

Clone this repository to your local environment:

```bash
git clone https://github.com/kubecost/gcp-marketplace-community.git
cd gcp-marketplace-community/kubecost/ 
```

#### Install the Application resource definition

An Application resource is a collection of individual Kubernetes components,
such as Services, Deployments, and so on, that you can manage as a group. the Kubernetes Application resource type, a custom resource definition (CRD) provided by the open source Kubernetes project to support GCP Marketplace and Application Deployment

To deploy Application CRD, run the following command:

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/application/master/deploy/kube-app-manager-aio.yaml
```

Learn more about Application CRD at [Preparing GKE](https://cloud.google.com/solutions/using-gke-applications-page-cloud-console#preparing_gke)

### Install Kubecost

#### Create namespace in your Kubernetes cluster

```bash
export NAMESPACE=kubecost
kubectl create namespace "$NAMESPACE"
```

#### Install the application with Helm (v3) to your Kubernetes cluster

Run the following command to deploy Kubecost from GCP Marketplace manually on your GKE cluster

```bash
# You can find the available tags at https://console.cloud.google.com/gcr/images/kubecost1/global/gcp-mp/cost-model
IMAGETAG=1.97.0
helm install kubecost ./chart/kubecost/ -n kubecost \
--set cost-analyzer.kubecostModel.fullImageName="gcr.io/kubecost1/gcp-mp/cost-model:${IMAGETAG}" \
--set cost-analyzer.kubecostFrontend.fullImageName="gcr.io/kubecost1/gcp-mp/cost-model/frontend:${IMAGETAG}" \
--set cost-analyzer.prometheus.server.image.repository="gcr.io/kubecost1/gcp-mp/cost-model/prometheus" \
--set cost-analyzer.prometheus.server.image.tag="${IMAGETAG}"
```

It takes less than 5 minutes for the installation to complete.

#### View the Kubecost dashboard in the web browser

Run the following command to forward the port for accessing locally on your web browser:

```bash
kubectl port-forward --namespace kubecost deployment/kubecost-cost-analyzer 9090
```

To view the app, open the URL `http://localhost:9090/` in your browser.

Depending on your organization’s requirements and set up, there are several options to expose Kubecost for on-going internal access. Check out the Kubecost documentation for Ingress Examples as a reference for using Nginx ingress controller with basic auth.
# Backup Kubecost

Learn more about backing up Kubecost data at [ETL backup](https://guide.kubecost.com/hc/en-us/articles/4407601811095-ETL-Backup)

To back up your configuration, run the following command:

```bash
helm get values kubecost -n kubecost >> kubecost-helm-values_$CURRENT_KUBECOST_VERSION.yaml
```

# Upgrade Kubecost

Set the new image version in an environment variable and run the following command:

```bash
export NEW_VERSION=<NEW_VERSION>
helm upgrade kubecost ./chart/kubecost/ -n kubecost \
--set cost-analyzer.kubecostModel.fullImageName="gcr.io/kubecost1/gcp-mp/cost-model:${IMAGETAG}" \
--set cost-analyzer.kubecostFrontend.fullImageName="gcr.io/kubecost1/gcp-mp/cost-model/frontend:${IMAGETAG}" \
--set cost-analyzer.prometheus.server.image.repository="gcr.io/kubecost1/gcp-mp/cost-model/prometheus" \
--set cost-analyzer.prometheus.server.image.tag="${IMAGETAG}"
```

# Uninstall Kubecost

## Using the Google Cloud Platform Console

1. In the GCP Console, open [Kubernetes Applications](https://console.cloud.google.com/kubernetes/application).

1. From the list of applications, click **Kubecost by Stackwatch,Inc.**.

1. On the Application Details page, click **Delete**.

## Using the command line

Delete the application release using Helm:

```bash
helm delete kubecost -n kubecost
kubectl delete Application kubecost -n kubecost
```
## License

This repository is licensed under Apache License 2.0 - see
[`LICENSE`](LICENSE) for more details.