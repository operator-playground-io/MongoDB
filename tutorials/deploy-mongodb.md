---
title: How Deploy MongoDB resource
description: This tutorial explains how to create the MongoDB resource. Follow the next section on how to work with Ops Manager resource.
---
### Adding Ops Manager Credentials

Adding Ops Manager Credentials

For the Operator to work, you will need the following information:

- Base URL - the URL of an Ops Manager instance (for Cloud Manager use https://cloud.mongodb.com)
- (optionally) Project Name - the name of an Ops Manager Project where MongoDBs will be deployed into. It will be created by the Operator if it doesn't exist (and this is the recommended way instead of reusing the project created in OpsManager directly). If omitted the name of the MongoDB resource will be used as a project name.
- (optionally) Organization ID - the ID of the organization which the Project belongs to. The Operator will create an Organization with the same name as the Project if Organization ID is omitted.
- API Credentials. This can be any pair of:
    - Public and Private Programmatic API keys. They correspond to user and publicApiKey fields in the Secret storing credentials. 
    - Username and Public API key. 

Note: that you must whitelist the Pod IP range of your Kubernetes cluster so that the Operator could make API requests to Ops Manager. You can get the Pod IP range of your kubernetes cluster by executing the command: kubectl cluster-info dump | grep -m 1 cluster-cidr

### Projects

A Project object is a Kubernetes ConfigMap that points to an Ops Manager installation and a Project. 
This ConfigMap has the following structure:

Note, that if orgId is skipped then the new organization named projectName will be automatically created and new project will be added there. If projectName is skipped the project created in Ops Manager will get the same name as the MongoDB object

```execute
cat <<'EOF' >my-project.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: my-project
  namespace: mongodb
data:
  projectName: myProjectName # this is an optional parameter
  orgId: 5b890e0feacf0b76ff3e7183 # this is an optional parameter
  baseUrl: https://my-ops-manager-or-cloud-manager-url
EOF
```

Execute below command to create new project:

```execute
kubectl apply -f my-project.yaml
```

You will see output similar below:

```
configmap/my-project created
```

Execute following command to verify your ConfigMap

```execute
kubectl describe configmaps my-project -n mongodb
```

You will see output similar below:

```
Name:         my-project
Namespace:    mongodb
Labels:       <none>
Annotations:  kubectl.kubernetes.io/last-applied-configuration:
                {"apiVersion":"v1","data":{"baseUrl":"https://my-ops-manager-or-cloud-manager-url","orgId":"5b890e0feacf0b76ff3e7183","projectName":"myPro...

Data
====
baseUrl:
----
https://my-ops-manager-or-cloud-manager-url
orgId:
----
5b890e0feacf0b76ff3e7183
projectName:
----
myProjectName
Events:  <none>
```

### Credentials

For a user to be able to create or update objects in this Ops Manager Project they need either a Public API Key or a Programmatic API Key. These will be held by Kubernetes as a Secret object. You can create this Secret with the following command:

```execute
kubectl -n mongodb create secret generic my-credentials --from-literal="user=my-public-api-key" --from-literal="publicApiKey=my-private-api-key"
```

You will see output similar below:

```
secret/my-credentials created
```

Execute the following Kubernetes command to verify your secret:

```execute
kubectl describe secrets/my-credentials -n mongodb
```

You will see output similar below:

```
Name:         my-credentials
Namespace:    mongodb
Labels:       <none>
Annotations:  <none>

Type:  Opaque

Data
====
publicApiKey:  18 bytes
user:          17 bytes
```


### Creating a MongoDB Resource

A MongoDB resource in Kubernetes is a MongoDB (short name mdb). We are going to create a replica set to test that everything is working as expected. 

```execute
cat <<'EOF' >replica-set.yaml
apiVersion: mongodb.com/v1
kind: MongoDB
metadata:
  name: my-replica-set
spec:
  members: 3
  version: 4.4.0-ent
  type: ReplicaSet

  opsManager:
    configMapRef:
      name: my-project
  credentials: my-credentials

  persistent: false

  podSpec:
    # 'podTemplate' allows to set custom fields in PodTemplateSpec.
    # (https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.17/#podtemplatespec-v1-core)
    # for the Database StatefulSet.
    podTemplate:
      spec:
        containers:
          - name: mongodb-enterprise-database
            resources:
              limits:
                cpu: "2"
                memory: 700M
              requests:
                cpu: "1"
                memory: 500M
EOF
```

To create replica set apply yml file which we created:

```execute
kubectl apply -f replica-set.yaml -n mongodb
```

You will see output similar below:

```
mongodb.mongodb.com/my-replica-set created
```

To check the status of your MongoDB Kubernetes resource, execute following command:

```execute
kubectl get mdb my-replica-set -o yaml -n mongodb
```

You will see output similar below:

```
apiVersion: mongodb.com/v1
kind: MongoDB
metadata:
  annotations:
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"mongodb.com/v1","kind":"MongoDB","metadata":{"annotations":{},"name":"my-replica-set","namespace":"mongodb"},"spec":{"credentials":"my-credentials","members":3,"opsManager":{"configMapRef":{"name":"my-project"}},"persistent":false,"podSpec":{"podTemplate":{"spec":{"containers":[{"name":"mongodb-enterprise-database","resources":{"limits":{"cpu":"2","memory":"700M"},"requests":{"cpu":"1","memory":"500M"}}}]}}},"type":"ReplicaSet","version":"4.4.0-ent"}}
  creationTimestamp: "2020-12-24T14:24:24Z"
  generation: 1
  name: my-replica-set
  namespace: mongodb
  resourceVersion: "3006968"
  selfLink: /apis/mongodb.com/v1/namespaces/mongodb/mongodb/my-replica-set
  uid: e30b349d-760a-49d8-8a63-20324fe775db
spec:
  credentials: my-credentials
  members: 3
  opsManager:
    configMapRef:
      name: my-project
  persistent: false
  podSpec:
    podTemplate:
      spec:
        containers:
        - name: mongodb-enterprise-database
          resources:
            limits:
              cpu: "2"
              memory: 700M
            requests:
              cpu: "1"
              memory: 500M
  type: ReplicaSet
  version: 4.4.0-ent
```

