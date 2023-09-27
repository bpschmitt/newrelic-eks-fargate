# newrelic-eks-fargate

## Prerequisites

- [eksctl](https://eksctl.io/)
- kubectl
- helm

## Cluster Build

```
eksctl create cluster -f cluster/cluster.yaml
```

## Install New Relic

```
export NEW_RELIC_LICENSE_KEY=<YOUR NEW RELIC LICENSE KEY>
export NEW_RELIC_CLUSTER_NAME=<YOUR CLUSTER NAME>
kubectl create ns newrelic
kubectl create secret generic newrelic-license-key -n newrelic --from-literal=license-key=$NEW_RELIC_LICENSE_KEY
helm install newrelic-bundle newrelic/nri-bundle -n newrelic --set global.cluster=$NEW_RELIC_CLUSTER_NAME --set global.customSecretName=newrelic-license-key --set global.customSecretLicenseKey=license-key -f values_example.yaml
```

## Add demo workloads

Create two namespaces.

```
kubectl create ns demo
kubectl create ns ec2test
```

This nginx instance should be scheduled onto a Fargate node.

```
kubectl apply -f demo-workloads/deployment-fargate.yaml -n demo
```

This nginx instance should be scheduled onto an EC2 node.

```
kubectl apply -f demo-workloads/deployment-ec2.yaml -n ec2test
```