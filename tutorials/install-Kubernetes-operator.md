---
title: MongoDB Tutorial to deploy MongoDB into Kubernetes clusters
description: This tutorial explains how to deploy MongoDB into Kubernetes clusters
---

### Installation

**Step 1:** Create Kubernetes Namespace

The Mongodb Enterprise Operator is installed, by default, into the mongodb Namespace, but this Namespace is not created automatically. To create this Namespace you should execute:

```execute
kubectl create namespace mongodb
```

If you plan on using any other Namespace, please make sure you update the yaml files' metadata.namespace attribute to point to your preferred Namespace. If using helm you need to override the namespace attribute with --set namespace=<..> during helm installation

Sample output:

```
namespace/mongodb created
```

**Step 2:** Installation using yaml files:

Create CustomResourceDefinitions

The CustomResourceDefinition (or crds) should be installed before installing the operator into your Kubernetes cluster. To do this, make sure you have logged into your Kubernetes cluster and that you can perform Cluster level operations:

```execute
kubectl apply -f https://raw.githubusercontent.com/mongodb/mongodb-enterprise-kubernetes/master/crds.yaml
```
This will create a new crd in your cluster, MongoDB. This new object will be the one used by the operator to perform the MongoDb operations needed to prepare each one of the different MongoDb types of deployments.

You will see output similar below:

```
customresourcedefinition.apiextensions.k8s.io/mongodb.mongodb.com created
customresourcedefinition.apiextensions.k8s.io/mongodbusers.mongodb.com created
customresourcedefinition.apiextensions.k8s.io/opsmanagers.mongodb.com created
clusterrole.rbac.authorization.k8s.io/mongodb-enterprise-operator-mongodb-webhook created

```

Operator Installation

This operator can also be installed using yaml files, in case you are not using Helm. You may apply the config directly from github clone this repo, and apply the file

```
kubectl apply -f https://raw.githubusercontent.com/mongodb/mongodb-enterprise-kubernetes/master/mongodb-enterprise.yaml
```

**Step 3** Verify that the Custom Resource Definitions installed successfully:

```execute
kubectl get crd/mongodb.mongodb.com -n mongodb
```

You will see output similar below:

```
NAME                  CREATED AT
mongodb.mongodb.com   2020-12-23T12:26:33Z
```