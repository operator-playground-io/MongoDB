# MongoDB
### Overview
The MongoDB Enterprise Kubernetes Operator enables easy deploy of the following applications into Kubernetes clusters:

- MongoDB - Replica Sets, Sharded Clusters and Standalones, with authentication, TLS and many more options.
- Ops Manager - our enterprise management, monitoring and backup platform for MongoDB. The Operator can install and manage Ops Manager in Kubernetes for you.

The Operator requires access to one of our database management tools - Ops Manager or Cloud Manager - to deploy MongoDB instances. You may run Ops Manager either inside or outside Kubernetes, or may use Cloud Manager (cloud.mongodb.com) instead.



**Requirements:**

To work with MongoDB resource this Operator requires Ops Manager (Ops Manager can be installed into the same Kubernetes cluster by the Operator or installed outside of the cluster manually) or Cloud Manager.

**MongoDB Enterprise Kubernetes Operator**

The MongoDB Enterprise Kubernetes Operator translates the human knowledge of creating a MongoDB instance into a scalable, repeatable, and standardized method. Kubernetes needs help creating and managing stateful applications like databases. It needs to configure the network, persist storage, and dedicate computing capacity without additional human effort on each container.

The Kubernetes Operator manages the typical lifecycle events for a MongoDB cluster: provisioning storage and computing power, configuring network connections, setting up users, and changing these settings as needed. It accomplishes this using the Kubernetes API and tools.

You provide the Operator with the specifications for your MongoDB cluster. The Operator uses this information to tell Kubernetes how to configure that cluster including provisioning storage, setting up the network connections, and configuring other resources.

![](_images/Architecture.png)

The MongoDB Enterprise Kubernetes Operator works together with MongoDB Cloud Manager or Ops Manager, which further configures to MongoDB clusters. When MongoDB is deployed and running in Kubernetes, you can manage MongoDB tasks using Cloud Manager or Ops Manager.

