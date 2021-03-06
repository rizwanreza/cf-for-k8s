_This is a highly experimental project to deploy the new CF Kubernetes-centric
components on Kubernetes. It is **not** meant for use in production and is subject to
change in the future._

# Deploying CF for K8s

## Prereqs

You need the following CLIs on your system to be able to run the script:
* [`kapp`](https://k14s.io/#install)
* [`helm`](https://github.com/helm/helm#install)
* [`ytt`](https://k14s.io/#install)

In addition, you will also probably want [`kubectl`](https://kubernetes.io/docs/tasks/tools/install-kubectl/) for your own debugging and inspection of the system.

Make sure your that your kuberntes config (e.g, `~/.kube/config`) is pointing to the cluster you intend to
deploy CF for K8s to. This cluster should be on an IaaS that supports load
balancer services (e.g., GKE, AKS, etc.).

## Steps to deploy

1. Git clone this repository and `cd` into this directory.
1. Update the submodules of this repository: `git submodule update --init --recursive`.
1. Deploy a database that can be used for the Cloud Controller's DB.
  - For example:
  ```
  helm repo add stable https://kubernetes-charts.storage.googleapis.com
  helm upgrade --install capi-database stable/postgresql -n default -f "charts/capi-k8s-release/scripts/postgresql-values.yaml"
  ```
1. Create a file called `cf-install-values.yml`. You can use `sample-cf-install-values.yml` in this directory as a starting point.
1. From this directory, run `bin/install-cf.sh <path to your cf-install-values.yml file>`
1. Configure DNS on your IaaS provider to point the wildcard subdomain of your
   system domain and the wildcard subdomain of all apps domains to point to external IP
   of the Istio Ingress Gateway service. You can retrieve the external IP of this service by running
   `kubectl get svc -n istio-system istio-ingressgateway -o jsonpath='{.status.loadBalancer.ingress[*].ip}'`
