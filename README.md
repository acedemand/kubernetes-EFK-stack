# kubernetes-EFK-stack
Up and running EFK stack on Kubernetes

This is a step by step guide about how to deploy EFK stack on Kubernetes.

EFK stack consists of:

- Elasticsearch
- Fluentd
- Kibana

Connect to a Kubernetes cluster and follow the steps below.

### Step 1 - Create a namespace.

```
kubectl create ns logging
```

### Step 2 - Install Elasticsearch

```
helm install incubator/elasticsearch --namespace logging --name elasticsearch --set image.tag=6.4.2,master.persistence.storageClass=fast-disk,data.persistence.storageClass=fast-disk
```

As you may notice, there should be a storage class named fast-disk for this setup to work.

### Step 3 - Install Fluentd

```
helm install --name fluentd --namespace logging stable/fluentd-elasticsearch --set elasticsearch.host=elasticsearch-client.logging.svc.cluster.local,elasticsearch.port=9200
```

### Step 4 - Install Kibana

```
helm install --name kibana --namespace logging stable/kibana --set env.ELASTICSEARCH_URL=http://elasticsearch-client.logging.svc.cluster.local:9200,service.type=NodePort,image.tag=6.4.2
```

Notice that, Kibana and Elasticsearch container image tags are compatible. 

### Step 5 - Configure Kibana

logstash* can be used for index pattern and @timestamp can be used for time filtering.

These are all the steps to have an up and running EFK stack on Kubernetes. One can deploy an application and make calls to service endpoint and all logs can be seen in Kibana interface.
