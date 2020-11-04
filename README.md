# Prereq helm charts for running applications on kubernetes cluster
This helm chart is using StatefulSet of Kubernetes-Kafka.

## Team Members:
1. Rajashree Joshi
1. Kinnar Kansara

## Reuirements
1. Kubernetes 1.3 with alpha APIs enabled and support for storage classes
1. PV support on underlying infrastructure
1. Requires at least v2.0.0-beta.1 version of helm to support dependency management with requirements.yaml

## StatefulSet Details
- https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/

## Incubator (Apache Kafka Helm Chart)
- https://github.com/helm/charts/tree/master/incubator/kafka

## Chart Details
This chart will do the following:
- Implement a dynamically scalable kafka cluster using Kubernetes StatefulSets
- Implement a dynamically scalable zookeeper cluster as another Kubernetes StatefulSet required for the Kafka cluster above

## Installing the chart in default namespace
```
helm repo add incubator http://storage.googleapis.com/kubernetes-charts-incubator --force-update

helm install kafka incubator/kafka -f helm/values.yaml
```

## Installing the chart with new namespace
To install the chart with the release name `kafka` in the `kafka` namespace:
```
helm repo add incubator http://storage.googleapis.com/kubernetes-charts-incubator --force-update

kubectl create ns kafka

helm install kafka --namespace kafka incubator/kafka -f helm/values.yaml
```

This will spin up the pods with mentioned configuration in the file `values.yaml`. 
Default configuration is like below:
- 3 Zookeeper pods
- 3 Kafka brokers

And two kafka topics would be created with names `watch` and `weather` with below configuration:
- 3 Partitions
- 2 Replication factor


## Verifying the installation
We can use the Kafka client image pulled from docker hub to test if the setup was successful and kafka is running properly.
```
kubectl apply -f kafka_client.yaml
```

Create a producer that will publish messages to the topic.
```
kubectl  exec -ti testclient -- ./bin/kafka-console-producer.sh --broker-list kafka:9092 --topic watch
```

Open consumer session which can consume the messages sent by producer.
```
kubectl exec -ti testclient -- ./bin/kafka-console-consumer.sh --bootstrap-server kafka:9092 --topic watch
```


### References
- https://github.com/helm/charts/tree/master/incubator/kafka
- https://medium.com/better-programming/how-to-run-highly-available-kafka-on-kubernetes-a1824db8a3e2