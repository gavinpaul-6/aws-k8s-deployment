##### Main K8s Components

* **Pod**
    - A **Pod** is the smallest and simplest Kubernetes object. It represents a single instance of a running process in a cluster. Pods can contain one or more containers, such as Docker containers, that share the same network and storage resources.
    - Smallest unit in Kubernetes
    - Abstraction over container
    - You only interact with the Kubernetes layer
    - Usually 1 application per Pod
    - Each Pod gets its own IP address (New IP on recreation)
    - Ephemeral (they die easily)

* **Service**
    - Permanent IP address
    - Lifecycle of Pod and Service **not connected**
    - Service is specified on creation

* **Ingress**
    -   An API object that manages external access to services in a cluster, typically HTTP. It provides HTTP routing and can also terminate SSL/TLS, enabling secure connections.


* **ConfigMap**
    - Is used to store configuration data in key-value pairs. This data can be used by Pods or other Kubernetes components to configure settings dynamically without hardcoding them into application code.
    - For **non-confidential** data only

* **Secret**
    - Used to store sensitive information, such as passwords, OAuth tokens, and ssh keys. Secrets are encoded in base64 format and are designed to be used by Pods to keep sensitive data secure.
    - The built-in security mechanism is not enabled by default
    - Secret components are meant to be encrypted using 3rd party tools in Kubernetes
    - Referenced in Deployment/Pod

* **Volume**
    - Attaches a physical storage on a hard drive
        - Storage on local machine
        - Or remote, outside of the K8s cluster
        - Kubernetes doesn't manage data persistence

* **Deployment**
    - Provides declarative updates to Pods and ReplicaSets. It ensures that a specified number of Pods are running at any given time and can handle updates to the Pods without downtime.
    - Blueprint for Pods
    - You create Deployments
    - Abstraction of Pods
    - Database cannot be replicated using Deployments
    - For Stateless apps
* **StatefulSet**
    - Is used for managing stateful applications. It provides guarantees about the ordering and uniqueness of Pods. Each Pod in a StatefulSet gets a stable, unique network identifier and persistent storage.
    - For Stateful apps (e.g. MySQL, Elastic, mongoDB, etc.)
    - DBs are often hosted outside of the K8s cluster
