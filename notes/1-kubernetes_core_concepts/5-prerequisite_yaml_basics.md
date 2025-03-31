# Pre-Requisite: YAML Basics

* YAML Ain't Markup Language
* YAML is a data serialization language
* Standard format to transfer data
* File extension: **.yaml** or **.yml**

## YAML Format compared to others

* Human readable and intuitive
* YAML is superset of JSON: any valid JSON  file is also a valid YAML file

## YAML Use Cases

* Docker Compose File
* Ansible
* Kubernetes


## YAML Syntax

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    app: nginx
spec:
    containers:
    - name: nginx-container
      image: nginx
```

* Basic Syntax of YAML:
    - ✅ key-value pairs
    - ✅ comments
    - ✅ objects
    - ✅ lists
    - ✅ boolesan