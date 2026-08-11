# Overview 
Kubernetes provides `kubectl` commands that can you help you inspect pods, review logs, and debug active containers when a workload does not perform as expected.  `kubectl' is the main command line interface (CLI) tool for running commands and managing Kubernetes clusters using the Kubernetes API server. These commands help with deploying an application, troubleshoot a problem, and generate health reports of a container. 

This is a quick reference document for some of the commonly-used debug commands for troubleshooting Kubernetes containers.  For more detailed information about installing and using `kubectl`, refer to the [`kubectl`documentation](https://kubernetes.io/docs/reference/kubectl/). 


# Assumptions
* If you are new to Kubernetes, it is highly recommended that you learn about basic kubectl commands and the relationships between concepts before you proceed with troubleshooting an active container.

* As an experienced user of Kubernetes, it is assumed you have a valid context to the desired `kubectl` command and permissions to view resources in the namespace you want to inspect. It also assumed that you have access to the cluster where the workload runs. 

..note::
Confirm you have the correct namespace before you troubleshoot. 



# Feedback
* Was this page helpful? 


