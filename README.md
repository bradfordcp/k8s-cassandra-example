# Apache Cassandra on Kubernetes with Prometheus & Grafana

## Tools
* K3d - 
    ```console
    $ k3d version
    k3d version v3.0.1
    k3s version v1.18.6-k3s1 (default)
    ```
* Helm -
    ```console
    $ helm version
    version.BuildInfo{Version:"v3.2.4", GitCommit:"0ad800ef43d3b826f31a5ad8dfbb4fe05d143688", GitTreeState:"clean", GoVersion:"go1.13.12"}
    ```

## Steps

```console
# Create the Kubernetes cluster
$ k3d cluster create

$ kubectl cluster-info
Kubernetes control plane is running at https://0.0.0.0:40227
CoreDNS is running at https://0.0.0.0:40227/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy
Metrics-server is running at https://0.0.0.0:40227/api/v1/namespaces/kube-system/services/https:metrics-server:/proxy

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.

# Install Helm repositories
$ helm repo add datastax https://datastax.github.io/charts
"datastax" has been added to your repositories
$ helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
"prometheus-community" has been added to your repositories
$ helm repo update

# Install Prometheus & Grafana
$ helm install monitoring prometheus-community/kube-prometheus-stack -f monitoring.values.yaml
NAME: monitoring
LAST DEPLOYED: Wed Jan  6 20:39:06 2021
NAMESPACE: default
STATUS: deployed
REVISION: 1
NOTES:
kube-prometheus-stack has been installed. Check its status by running:
  kubectl --namespace default get pods -l "release=monitoring"

Visit https://github.com/prometheus-operator/kube-prometheus for instructions on how to create & configure Alertmanager and Prometheus instances using the Operator.

# Install cass-operator
$ helm install cass-operator datastax/cass-operator
NAME: cass-operator
LAST DEPLOYED: Wed Jan  6 20:30:48 2021
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None

# Provision Cassandra cluster
$ kubectl apply -f cluster.yaml
cassandradatacenter.cassandra.datastax.com/dc1 created

# Create service monitor and dashboards for monitoring stack
$ kubectl create -f dashboards.configmaps.yaml
$ kubectl apply -f cluster.servicemonitor.yaml
```
