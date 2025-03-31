# Manage Kubernetes Resources - kubectl and config file

* How to **create** those components

1) Kubernetes CLI: kubectl
    - Very powerful tool that **enables interaction with the cluster
    - **Limitation** when using those commands

2) K8s Configuration Files
    - Write the component configuration in a file
    - **Apply configuration file with kubectl
    - **Update** K8s components:
        Edit config file and apply again
    - **Delete** K8s componenets with config file
 ```yaml
  apiVersion: v1
  kind: Deployment 
  metadata:
      name: nginx-deployment
      labels:
          app: nginx
  spec:
    replicas: 2
    selector:
        matchLabels:
            name: nginx 
    template:
        metadata:
            labels:
                app: nginx
          spec:
              containers:
              - name: nginx-container
                  image: nginx
  ```

## Imperative vs declarative

* **Imperative**
    - Telling K8s **WHAT to do** (e.g. create, delete, update...)
    - We operate directly on live objects
    - Practical when testing
    - Quick for one-off tasks or when just getting started
**
* **Declarative**
    - Telling K8s **WHAT we want as an end result** in the config file
    - We operate on object configuration files
    - History of configurations
    - IaC in Git Repo
    - Collaboration and review processes possible
    - More transparent
