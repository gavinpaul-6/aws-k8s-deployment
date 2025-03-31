# Kubernetes Architecture (Control Plane and Worker nodes)

* Container Runtime
* Kubelet
*   - Interacts with both: the container and node
    - Starts the pod with the container inside
* Services
    - Communication between nodes (e.g. DB service)

* Kube proxy
    - Forwards the requests


## Control Plan Processes

* 4 Processes

    * **API Server**
        - Cluster gateway (Updates/Query)
        - Acts as a gatekeeper for communication

    * **Scheduler**
        - Schedules new Pod
        - Determines where to put the pod based on resource usage

    * **Controller Manager**
        - Detect cluster state changes

    * **Etcd**
        - Cluster brain
        - Gets stored in the key value store
        - Application data is **NOT** stored in etcd

- Multiple Control Plane nodes are API server **load balanced** and etcd **distributed storage** across all master nodes
