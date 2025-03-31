# Kubernetes Configuration File

* The **3 parts** of configuration file
* **Connecting** Deployment to Service to Pod

    1) Metadata
    2) Specification
        - Attributes of "spec" are **specific** to the **kind**
        - E.g. Deployment and Service will have their own atrributes

    3) Status
        - Desired? = Actual?


### Where does K8s get this status data?

* Etcd holds the current status of any K8s component


### YAML Configuration Files

- **""human friendly** data serialzation standard for all programming languages

- Syntax: **strict indentation**

- Store the config file with your code