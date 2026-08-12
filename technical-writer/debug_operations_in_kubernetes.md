# Overview 
Kubernetes provides `kubectl` commands that can you help you inspect pods, review logs, and debug active containers when a workload does not perform as expected.  `kubectl' is the main command line interface (CLI) tool for running commands and managing Kubernetes clusters using the Kubernetes API server. These commands help with deploying an application, troubleshooting a problem, and generating health reports of a container. 

This document is a quick reference guide for some of the commonly-used debug commands for troubleshooting Kubernetes containers.  For more detailed information about installing and using `kubectl`, refer to the [`kubectl`documentation](https://kubernetes.io/docs/reference/kubectl/). 


## Assumptions
* If you are new to Kubernetes, it is highly recommended that you learn about basic kubectl commands and the relationships between concepts before you proceed with troubleshooting an active container.

* As an experienced user of Kubernetes, it is assumed you have a valid context to the desired `kubectl` command and permissions to view resources in the namespace you want to inspect. It is also assumed that you have access to the cluster where the workload runs. 

> [!NOTE]
> Confirm you have the correct namespace before you troubleshoot.

## Commonly-used `kubectl` commands
This section provides a list of commonly-used `kubectl` instructions that enable new and experienced users to perform basic tasks like viewing pod status in a namespace, review container logs, inspect the container environment, and the option to use advanced debug tools. 

Before you dive into the commands, it is important to understand the three foundational concepts in Kubernetes:

* **Pod**: A pod is the smallest Kubernetes object and represents a set of running containers on your cluster.
* **Namespace**: An abstraction used by Kubernetes to support isolation of groups of API resources within a single cluster.
* **Node**: A node is a physical or a virtual machine in a cluster that runs workloads.

For more detailed list of Kubernetes terms and definitions, refer to the [Kubernetes Glossary](https://kubernetes.io/docs/reference/glossary/?fundamental=true)

### View Pod Status
Use the `kubectl get pods` instruction to view pods. You must specify the namespace to identify workloads that are healthy or those thay may need attention. 

For example, 

To list pods in a specific namespace, use 

```bash
kubectl get pods --namespace <namespace-name>
```

To list all pods, use the following command
```bash
kubectl get pods --all-namespaces
```
> [!NOTE]
> If you omit the namespace, the `kubectl get` command defaults to the namespace that is set to the current context. 

For a comprehensive list of `kubectl get commands`, refer to [kubectl_get](https://kubernetes.io/docs/reference/kubectl/generated/kubectl_get/)

### 


References
* [Kubernetes Glossary](https://kubernetes.io/docs/reference/glossary/?fundamental=true)
* [kubectl_get](https://kubernetes.io/docs/reference/kubectl/generated/kubectl_get/)





# Feedback
Was this page helpful? 


