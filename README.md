# Kubernetes Cluster Setup and CKA Exam Documentation

Welcome to the documentation for setting up and administering a Kubernetes cluster on AWS! In this repository, I will be documenting the process of deploying and managing a Kubernetes cluster from scratch using **three EC2 instances**. This setup will be done without using any managed services like Amazon EKS. Additionally, this repository will cover tasks related to the **Certified Kubernetes Administrator (CKA)** exam.

## Contents

This repository will include the following:

1. **Kubernetes Cluster Setup from Scratch on AWS**  
   - A guide for setting up a Kubernetes cluster on AWS using three EC2 instances, configuring the cluster components manually.
   
2. **CKA Exam Preparation**  
   - A collection of tasks and topics based on the official CKA exam curriculum.
   - Hands-on lab exercises and practical examples to help prepare for the CKA exam.

3. **Administering Kubernetes in AWS**  
   - Best practices for monitoring, scaling, and managing a Kubernetes cluster in AWS.
   - Security, networking, and troubleshooting in a production environment.

4. **Useful Commands and Tools**  
   - Common `kubectl` commands, AWS CLI commands, and other useful tools for Kubernetes cluster administration.

## Purpose

The goal of this repository is to provide a clear and detailed guide for setting up a Kubernetes cluster from scratch on AWS using three EC2 instances, while documenting the knowledge and skills required to pass the **Certified Kubernetes Administrator (CKA)** exam. Whether you're new to Kubernetes or preparing for the exam, this documentation will help you build and maintain a Kubernetes cluster in a cloud environment.

## Getting Started

### Prerequisites

- An active AWS account
- Basic understanding of Kubernetes concepts and architecture
- AWS CLI and `kubectl` installed and configured
- Familiarity with Linux command-line tools
- Three EC2 instances running Ubuntu (or your preferred OS) to form the cluster nodes

### Cluster Setup

In this section, I will document the process of setting up a Kubernetes cluster using **three EC2 instances**. This includes:

- Launching EC2 instances and configuring the necessary network settings.
- Installing Kubernetes components (e.g., `kubeadm`, `kubectl`, `kubelet`) on all instances.
- Setting up the Kubernetes master node and joining the worker nodes to the cluster.
- Configuring networking and ensuring communication between the nodes.
  
This setup will be done manually without relying on managed services such as EKS, to give a hands-on understanding of Kubernetes cluster management.

### CKA Exam Tasks

This section will include tasks to help you prepare for the **CKA Exam**:

- **Cluster Architecture**: Designing a multi-node cluster, managing namespaces, and configuring role-based access control (RBAC).
- **Installation and Configuration**: Installing and configuring Kubernetes components (from scratch), ensuring high availability, and managing the lifecycle of the cluster.
- **Workloads and Scheduling**: Managing Pods, Deployments, and Jobs; configuring resource limits and requests; scheduling applications.
- **Services and Networking**: Exposing services, managing DNS, setting up ingress controllers, and configuring network policies.
- **Monitoring and Logging**: Setting up monitoring and logging solutions (e.g., Prometheus, Fluentd) for Kubernetes clusters.
- **Troubleshooting**: Diagnosing issues with Pods, Nodes, and Services; examining logs, events, and other debugging tools.

## Contributing

If you would like to contribute to this documentation or suggest improvements, please feel free to submit a pull request.

