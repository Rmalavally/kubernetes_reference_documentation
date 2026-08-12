# Overview 
Kubernetes (K8s) provides `kubectl`, a main command line interface (CLI) tool for running commands and managing Kubernetes clusters using the Kubernetes API server. These commands help with deploying an application, troubleshooting a problem, and checking the status of a container. 
This document is a quick reference guide for the following `kubectl` commands used in troubleshooting Kubernetes containers. 

> [!NOTE]
> It is recommended you use these commands in the following order when debugging workloads. 

* `kubectl get pods`: View pod status.
* `kubectl logs`: Retrieve and review logs for a container in a pod.
* `kubectl exec`: Execute a command to open an interactive session inside the container.
* `kubectl debug`: Clone a pod with the same environment without changing the original container and use advanced debug options when a container is in a CrashLoopBackOff state. 
 
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

For a detailed list of Kubernetes terms and definitions, refer to the [Kubernetes Glossary](https://kubernetes.io/docs/reference/glossary/?fundamental=true)

### View Pod Status
Use the `kubectl get pods` instruction to view pods. Ensure you specify the namespace to identify workloads that are healthy or those thay may need attention. 

To list pods in a specific namespace, use 

```bash
kubectl get pods --namespace <namespace-name>
```

To list all pods, use the following command
```bash
kubectl get pods --all-namespaces
```

**Example**
```bash
kubectl get pods --namespace kube-system
```

**Expected output**

```bash
root@controlplane:~$ kubectl get pods --namespace kube-system
NAME                                   READY   STATUS    RESTARTS      AGE
cilium-7l688                           1/1     Running   1 (65m ago)   22d
cilium-envoy-tc5rr                     1/1     Running   1 (65m ago)   22d
cilium-operator-768c98966f-9n259       1/1     Running   2 (65m ago)   22d
coredns-5f68d5bd7f-cl5xn               1/1     Running   1 (65m ago)   22d
coredns-5f68d5bd7f-qrs4d               1/1     Running   1 (65m ago)   22d
etcd-controlplane                      1/1     Running   1 (65m ago)   22d
kube-apiserver-controlplane            1/1     Running   1 (65m ago)   22d
kube-controller-manager-controlplane   1/1     Running   1 (65m ago)   22d
kube-scheduler-controlplane            1/1     Running   1 (65m ago)   22d
```

The output of the `kubectl get pods` command displays the name, readiness, current status, restarts. and age amongst other fields. 

> [!NOTE]
> If you omit the namespace, the `kubectl get` command defaults to the namespace that is set to the current context.

> [!NOTE]
> If no pods exist in the namespace, you will see a message indicating no resources were found in the specific namespace.

> [!NOTE]
> If the `--namespace` is missing a value, the following CLI usage error displays:

```bash
root@controlplane:~$ kubectl get pods --namespace
error: flag needs an argument: --namespace
See 'kubectl get --help' for usage.
```

**Useful links**

* For a comprehensive list of `kubectl get commands`, refer to [kubectl_get.](https://kubernetes.io/docs/reference/kubectl/generated/kubectl_get/)

* For detailed documentation on Pods, refer to [Pods.](https://kubernetes.io/docs/concepts/workloads/pods/)

### Review Container Logs
Use the `kubectl logs` command to print the logs for a container in a pod. If a pod has only one container, the container name is optional. 

```bash
kubectl logs [-f] [-p] (POD | TYPE/NAME) [-c CONTAINER]
```
The command consists of the following syntax,

* `-f`: optional flag "follow" - for streaming logs live.
* `-p`: optional flag "previous" - displays logs from a previous instance of the container.
* `(POD | TYPE/NAME)`: is required, you must provide either a pod name or a specific resource.
* `[-c CONTAINER]`: optional, if the the pod has only one container.

**Example**
```bash
kubectl logs coredns-5f68d5bd7f-cl5xn --namespace kube-system
```

**Expected output**
```bash
$ kubectl logs coredns-5f68d5bd7f-cl5xn --namespace kube-system
maxprocs: Leaving GOMAXPROCS=1: CPU quota undefined
.:53
[INFO] plugin/reload: Running configuration SHA512 = 1b226df79860026c6a52e67daa10d7f0d57ec5b023288ec00c5e05f93523c894564e15b91770d3a07ae1cfbe861d15b37d4a0027e69c546ab112970993a3b03b
CoreDNS-1.13.1
linux/amd64, go1.25.2, 1db4568
```

> [!NOTE]
> If the pod does not exist, the `kubectl logs` command displays a NotFound error.

```bash
root@controlplane:~$ kubectl logs coredns-5d78c9869d-abcde --namespace kube-system
error: error from server (NotFound): pods "coredns-5d78c9869d-abcde" not found in namespace "kube-system"
```

**Useful links**

For more information about kubectl logs, refer to [kubectl logs documentation.](https://kubernetes.io/docs/reference/kubectl/generated/kubectl_logs/)


### Execute a command in a container
Use `kubectl exec` to run the command in an active container for information about the environment. 

```bash
kubectl exec (POD | TYPE/NAME) [-c CONTAINER] [flags] -- COMMAND [args...]
```
The command consists of the following syntax,
* `(POD | TYPE/NAME)`: is required, you must provide either a pod name or a specific resource.
* `[-c CONTAINER]`: if omitted, the default or contextual container is used. 
* `[flags]`: Use -i and -t for interactive sessions. 
* `-- `: you must use two dashes (--) to separate your command's flags/arguments.
* `COMMAND [args...]`: required, it is the command to execute inside the container.

**Example**

```bash
 kubectl exec coredns-5f68d5bd7f-cl5xn --namespace kube-system -- /coredns -version
```

**Expected output**
```bash
root@controlplane:~$ kubectl exec coredns-5f68d5bd7f-cl5xn --namespace kube-system -- /coredns -version
CoreDNS-1.13.1
linux/amd64, go1.25.2, 1db4568
```
> [!NOTE]
> Running the following command resulted in an error:

```bash
kubectl exec etcd-controlplane --namespace kube-system -- date
```

```bash
root@controlplane:~$ kubectl exec etcd-controlplane --namespace kube-system -- date
error: Internal error occurred: Internal error occurred: error executing command in container: failed to exec in container: failed to start exec "7519be462de5c3c32b2f168c35bc6bcb1d5a979a98742789d43767499904ef2d": OCI runtime exec failed: exec failed: unable to start container process: exec: "date": executable file not found in $PATH
root@controlplane:~$ 
```

**Useful links**
* For more details about using `kubectl exec`, refer to [kubectl exec.](https://kubernetes.io/docs/reference/kubectl/generated/kubectl_exec/)

# References
* [`kubectl` documentation](https://kubernetes.io/docs/reference/kubectl/)
* [Kubernetes Glossary](https://kubernetes.io/docs/reference/glossary/?fundamental=true)






