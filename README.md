# gcp-marketplace

This is the repo for code that is related to Kubecost GCP marketplace offers.

See [kubecost/README.md](kubecost/README.md) for more information.

## Update Notes

```sh
https://console.cloud.google.com/producer-portal/overview?project=kubecost-public 
https://console.cloud.google.com/marketplace/browse?project=guestbook-227502&q=kubecost 

# Clone the repo and create new branch
git clone https://github.com/kubecost/gcp-marketplace.git
cd gcp-marketplace/kubecost/
git checkout -b v1.106.2

#Set up ENV
# Ensure Application CRD is installed first (error message from verify.log: strict decoding error: unknown field): https://cloud.google.com/solutions/using-gke-applications-page-cloud-console#preparing_gke

gcloud config set project kubecost1
gcloud auth configure-docker
gcloud container clusters get-credentials demo-cluster --zone us-central1-c --project guestbook-227502
export IMAGETAG=prod-1.107.1
export MPIMAGETAG='1.107.1'
export DEPLOYERTAG='1.107'
# Install MPDEV
https://github.com/GoogleCloudPlatform/marketplace-tools/tree/master 

# Clone images kubecost byol
skopeo copy -a docker://gcr.io/kubecost1/cost-model:$IMAGETAG docker://gcr.io/kubecost1/gcp-mp/cost-model:$MPIMAGETAG
skopeo copy -a docker://gcr.io/kubecost1/cost-model:$IMAGETAG docker://gcr.io/kubecost1/gcp-mp/cost-model:$DEPLOYERTAG
skopeo copy -a docker://gcr.io/kubecost1/frontend:$IMAGETAG docker://gcr.io/kubecost1/gcp-mp/cost-model/frontend:$MPIMAGETAG
skopeo copy -a docker://gcr.io/kubecost1/frontend:$IMAGETAG docker://gcr.io/kubecost1/gcp-mp/cost-model/frontend:$DEPLOYERTAG
skopeo copy -a docker://quay.io/prometheus/prometheus:v2.35.0 docker://gcr.io/kubecost1/gcp-mp/cost-model/prometheus:$MPIMAGETAG
skopeo copy -a docker://quay.io/prometheus/prometheus:v2.35.0 docker://gcr.io/kubecost1/gcp-mp/cost-model/prometheus:$DEPLOYERTAG


# Clone images kubecost ENT
skopeo copy -a docker://gcr.io/kubecost1/cost-model:$IMAGETAG docker://gcr.io/kubecost1/gcp-mp/ent/cost-model:$MPIMAGETAG
skopeo copy -a docker://gcr.io/kubecost1/cost-model:$IMAGETAG docker://gcr.io/kubecost1/gcp-mp/ent/cost-model:$DEPLOYERTAG
skopeo copy -a docker://gcr.io/kubecost1/frontend:$IMAGETAG docker://gcr.io/kubecost1/gcp-mp/ent/cost-model/frontend:$MPIMAGETAG
skopeo copy -a docker://gcr.io/kubecost1/frontend:$IMAGETAG docker://gcr.io/kubecost1/gcp-mp/ent/cost-model/frontend:$DEPLOYERTAG
skopeo copy -a docker://quay.io/prometheus/prometheus:v2.35.0 docker://gcr.io/kubecost1/gcp-mp/ent/cost-model/prometheus:$MPIMAGETAG
skopeo copy -a docker://quay.io/prometheus/prometheus:v2.35.0 docker://gcr.io/kubecost1/gcp-mp/ent/cost-model/prometheus:$DEPLOYERTAG

# Update the version:
## kubecost byol
cd gcp-marketplace/kubecost
Delete requirements.lock
Delete charts/cost-analyzer-previousversion.tgz
Update kubecost/templates/application.yaml
Update kubecost/requirements.yaml
Update kubecost/Chart.yaml
Update kubecost/Schema.yaml
Run the command below:
helm dependency build chart/kubecost

### Build Deployer image and push it into GCR (Multi-arch image is not supported for the deployer)
docker build -t gcr.io/kubecost1/gcp-mp/cost-model/deployer:$DEPLOYERTAG -f deployer/Dockerfile .
docker push gcr.io/kubecost1/gcp-mp/cost-model/deployer:$DEPLOYERTAG
### Verification command: - no longer works as of mpdev 0.4.0
mpdev verify --deployer=gcr.io/kubecost1/gcp-mp/cost-model/deployer:$DEPLOYERTAG
### logs location
/home/myuser/.mpdev_logs/
### test deployment - no longer works as of mpdev 0.4.0
mpdev install --deployer=gcr.io/kubecost1/gcp-mp/cost-model/deployer:$DEPLOYERTAG  --parameters='{"name": "kubecost-myname", "namespace": "myname}'
### Clean up
kubectl delete application kubecost -n kubecost


## kubecost enterprise
cd gcp-marketplace/kubecost_paid
Delete requirements.lock
Delete charts/cost-analyzer-previousversion.tgz
Update kubecost/templates/application.yaml
Update kubecost/requirements.yaml
Update kubecost/Chart.yaml
Update kubecost/Schema.yaml
Run the command below:
helm dependency build chart/kubecost

### Build Deployer image and push it into GCR (Multi-arch image is not supported for the deployer)
docker build -t gcr.io/kubecost1/gcp-mp/ent/cost-model/deployer:$DEPLOYERTAG -f deployer/Dockerfile .
docker push gcr.io/kubecost1/gcp-mp/ent/cost-model/deployer:$DEPLOYERTAG
### Verification command:
mpdev verify --deployer=gcr.io/kubecost1/gcp-mp/ent/cost-model/deployer:$DEPLOYERTAG
## logs location
/home/myuser/.mpdev_logs/
### test deployment
mpdev install --deployer=gcr.io/kubecost1/gcp-mp/ent/cost-model/deployer:$DEPLOYERTAG  --parameters='{"name": "kubecost-myuser", "namespace": "myuser"}' 
### Clean up
kubectl delete application kubecost -n kubecost
### Following this process to update the listing: https://cloud.google.com/marketplace/docs/partners/kubernetes/maintaining-product
### Producer portal https://console.cloud.google.com/producer-portal/overview?project=kubecost-public 
### If there are any issues or if you need support from GCP Marketplace, contacting them at: cloud-partner-onboarding@google.com
### Push your changes to the repository once the new version is successfully approved and published
git push -u origin myuser/v1.100
```
