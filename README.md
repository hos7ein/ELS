# ELS
> The Elasticsearch deployment

## Table of contents
* [Introduction](#introduction)
* [Requirements](#requirements)
* [Install ECK operator](#Install-ECK-operator)
* [Deploy Elsticsearch instance](#Deploy-Elsticsearch-instance)
* [Request Elasticsearch access](#Request-Elasticsearch-access)
* [Contributing](#contributing)
* [Contact](#contact)
* [License](#license)

# Introduction

[Elastic Cloud on Kubernetes (ECK)](https://operatorhub.io/operator/elastic-cloud-eck) is the official operator by Elastic, a new orchestration product based on the Kubernetes Operator pattern that lets users provision, manage, and operate Elasticsearch clusters on Kubernetes that contains Elasticsearch, Kibana and APM Server.

## Requirements

* Kubernetes cluster
* kubectl
* helm
* ArgoCD (optional)

## Install ECK operator

To install ECK operator in your kubernetes cluster you can run this command:

```sh
git clone git@github.com:hos7ein/ELS.git
```

```sh
cd helm
```

```sh
helm install eck --debug  ./eck-operator/
```

## Deploy Elsticsearch instance

In this section, we'll walk you through the process of deploying an Elasticsearch cluster. Apply a simple Elasticsearch cluster specification, with three Elasticsearch nodes:

* You must always have at least one node that is master eligible, but ideally 3.
* Assigning a node the data role means that it can hold data. You must have at least one node that can hold data as well, otherwise, you will have nothing to search. A data node can also be master eligible, but that has nothing to do with whether it holds data or not.
* If you want to configure APM Server, you need ingest node to pre-process documents before indexing them in Elasticsearch.

By default, each node can play the 3 roles, create the Elasticsearch cluster with manifest and the Operator automatically creates and manages the resources needed to achieve the desired state.

```sh
helm install  es --debug ./es/
```

## Request Elasticsearch access

A ClusterIP Service is automatically created for your cluster:

```sh
kubectl -n els get service elasticsearch-sample-es-http
```

1. Get the credentials.
A default user named `elastic` is automatically created with the password stored in a Kubernetes secret:

```sh
PASSWORD=$(kubectl -n els  get secret elasticsearch-sample-es-elastic-user -o=jsonpath='{.data.elastic}' | base64 --decode)
```

2. Request the Elasticsearch endpoint.
From inside the Kubernetes cluster:

```sh
curl -u "elastic:$PASSWORD" -k "https://elasticsearch-sample-es-http:9200"
```

From your local workstation, use the following command in a separate terminal:

```sh
kubectl port-forward -n els service/elasticsearch-sample-es-http 9200
```

Then request localhost:

```sh
curl -u "elastic:$PASSWORD" -k "https://localhost:9200"
```


## Contributing
Pull requests are welcome. For major changes, please open an issue first to discuss what you would like to change.

## Contact

**Project website**: https://github.com/hos7ein/ELS

**Personal website**: https://fedorafans.com

**Author**: Hossein Aghaie <hossein.a97@gmail.com>

**Twitter**: Hossein Aghaie [@hos7ein](https://twitter.com/hos7ein)

## License

`ELS` source code is available under the GPL-3.0 [License](/LICENSE).
