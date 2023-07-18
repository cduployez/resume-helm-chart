# Tutorial

## Deploy tutorial app

Run hello-app in the cluster
```sh
kubectl create deployment hello-server \
    --image=us-docker.pkg.dev/google-samples/containers/gke/hello-app:1.0
```

Output:
```
Warning: autopilot-default-resources-mutator:Autopilot updated Deployment default/hello-server: defaulted unspecified resources for containers [hello-app] (see http://g.co/gke/autopilot-defaults)
deployment.apps/hello-server created
```

Expose the deployment:
```sh
kubectl expose deployment hello-server \
    --type LoadBalancer \
    --port 80 \
    --target-port 8080
```

Output:
```sh
service/hello-server exposed
```

Verify pod is deployed and running:
```sh
kubectl get pods
```

Output:
```sh
➜  ~ kubectl get pods
NAME                           READY   STATUS    RESTARTS   AGE
hello-server-7b48b76d8-rzqwp   1/1     Running   0          2m26s
```

Verify the load balancer is up and running:
```sh
kubectl get service hello-server
```

Output:
```sh
NAME           TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)        AGE
hello-server   LoadBalancer   10.89.2.115   34.163.40.252   80:31645/TCP   58s
```

Check result: http://34.163.40.252

## Cleanup tutorial app

```sh
kubectl delete service hello-server
```
