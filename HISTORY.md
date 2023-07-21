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

# Nginx Ingress controller

Links: 
- https://cloud.google.com/community/tutorials/nginx-ingress-gke
- https://medium.com/bluekiri/deploy-a-nginx-ingress-and-a-certitificate-manager-controller-on-gke-using-helm-3-8e2802b979ec

```shell
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
# "ingress-nginx" has been added to your repositories
helm repo update
# Hang tight while we grab the latest from your chart repositories...
# ...Successfully got an update from the "ingress-nginx" chart repository
# Update Complete. ⎈Happy Helming!⎈
```

```sh
helm install resume-ingress-controller ingress-nginx/ingress-nginx
```

Output:
```
W0718 12:28:42.672527    8490 warnings.go:70] autopilot-default-resources-mutator:Autopilot updated Job default/resume-ingress-controller-ingress-nginx-admission-create: defaulted unspecified resources for containers [create] (see http://g.co/gke/autopilot-defaults)
W0718 12:28:50.435314    8490 warnings.go:70] autopilot-default-resources-mutator:Autopilot updated Deployment default/resume-ingress-controller-ingress-nginx-controller: defaulted unspecified resources for containers [controller] (see http://g.co/gke/autopilot-defaults)
W0718 12:28:53.427516    8490 warnings.go:70] autopilot-default-resources-mutator:Autopilot updated Job default/resume-ingress-controller-ingress-nginx-admission-patch: defaulted unspecified resources for containers [patch] (see http://g.co/gke/autopilot-defaults)
NAME: resume-ingress-controller
LAST DEPLOYED: Tue Jul 18 12:28:38 2023
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
The ingress-nginx controller has been installed.
It may take a few minutes for the LoadBalancer IP to be available.
You can watch the status by running 'kubectl --namespace default get services -o wide -w resume-ingress-controller-ingress-nginx-controller'

An example Ingress that makes use of the controller:
  apiVersion: networking.k8s.io/v1
  kind: Ingress
  metadata:
    name: example
    namespace: foo
  spec:
    ingressClassName: nginx
    rules:
      - host: www.example.com
        http:
          paths:
            - pathType: Prefix
              backend:
                service:
                  name: exampleService
                  port:
                    number: 80
              path: /
    # This section is only required if TLS is to be enabled for the Ingress
    tls:
      - hosts:
        - www.example.com
        secretName: example-tls

If TLS is enabled for the Ingress, a Secret containing the certificate and key must also be provided:

  apiVersion: v1
  kind: Secret
  metadata:
    name: example-tls
    namespace: foo
  data:
    tls.crt: <base64 encoded cert>
    tls.key: <base64 encoded key>
  type: kubernetes.io/tls
```

# Deploy Kubernetes Dashboard

```sh
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.7.0/aio/deploy/recommended.yaml
```

Output:
```
namespace/kubernetes-dashboard created
serviceaccount/kubernetes-dashboard created
service/kubernetes-dashboard created
secret/kubernetes-dashboard-certs created
secret/kubernetes-dashboard-csrf created
secret/kubernetes-dashboard-key-holder created
configmap/kubernetes-dashboard-settings created
role.rbac.authorization.k8s.io/kubernetes-dashboard created
clusterrole.rbac.authorization.k8s.io/kubernetes-dashboard created
rolebinding.rbac.authorization.k8s.io/kubernetes-dashboard created
clusterrolebinding.rbac.authorization.k8s.io/kubernetes-dashboard created
Warning: autopilot-default-resources-mutator:Autopilot updated Deployment kubernetes-dashboard/kubernetes-dashboard: defaulted unspecified resources for containers [kubernetes-dashboard] (see http://g.co/gke/autopilot-defaults)
deployment.apps/kubernetes-dashboard created
service/dashboard-metrics-scraper created
Warning: autopilot-default-resources-mutator:Autopilot updated Deployment kubernetes-dashboard/dashboard-metrics-scraper: defaulted unspecified resources for containers [dashboard-metrics-scraper] (see http://g.co/gke/autopilot-defaults)
deployment.apps/dashboard-metrics-scraper created
```

Access at: http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/

Retrieve authentication token from:
```sh
gcloud config config-helper --format=json | jq -r '.credential.access_token'
```
