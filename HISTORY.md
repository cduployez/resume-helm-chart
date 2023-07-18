# History

Link: https://cloud.google.com/kubernetes-engine/docs/deploy-app-cluster

## Enable APIs

Enable project APIs in the above link

## Switch to "back-resume" project (= context)

```sh
gcloud config set project back-resume
```

## Create an Autopilot cluster for "resume" project containers

```sh
# Location: Paris, France (europe-west9)
gcloud container clusters create-auto resume-cluster --location=europe-west9
```

Output:
```
Note: The Pod address range limits the maximum size of the cluster. Please refer to https://cloud.google.com/kubernetes-engine/docs/how-to/flexible-pod-cidr to learn how to optimize IP address allocation.
Creating cluster resume-cluster in europe-west9... Cluster is being health-checked (master is healthy)...done.
Created [https://container.googleapis.com/v1/projects/back-resume/zones/europe-west9/clusters/resume-cluster].
To inspect the contents of your cluster, go to: https://console.cloud.google.com/kubernetes/workload_/gcloud/europe-west9/resume-cluster?project=back-resume
kubeconfig entry generated for resume-cluster.
NAME            LOCATION      MASTER_VERSION   MASTER_IP     MACHINE_TYPE  NODE_VERSION     NUM_NODES  STATUS
resume-cluster  europe-west9  1.26.5-gke.1200  34.163.80.76  e2-medium     1.26.5-gke.1200  3          RUNNING
```

## Get authentication credentials for the cluster

```sh
gcloud container clusters get-credentials resume-cluster --location=europe-west9
```

Output:
```sh
Fetching cluster endpoint and auth data.
kubeconfig entry generated for resume-cluster.
```

## Create repository in Artifact Registry

```sh
gcloud artifacts repositories create resume-repository \
    --project=back-resume \
    --repository-format=docker \
    --location=europe-west9 \
    --description="Docker repository for resume project"
```

Output:
```
Create request issued for: [resume-repository]
Waiting for operation [projects/back-resume/locations/europe-west9/operations/eb75cd55-5ab7-4748-a10c-b90c22f0ee21] to complete...done.
Created repository [resume-repository]
```
