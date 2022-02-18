---
title: "Kubernetes"
linkTitle: "Kubernetes"
weight: 1
---

> ⚠️ Make sure that the
> [prerequisites for Ondat](/docs/prerequisites) are
> satisfied before proceeding.

> ⚠️ Make sure to add a Ondat licence after installing.

> 💡 Any Kubernetes managed service such as EKS, AKS, GKE, DO or DockerEE
> platform can use the following Kubernetes guide to install Ondat.

> 💡 Ondat supports the five most recent Kubernetes releases, at minimum.

> 💡 Ondat also has an official Helm chart, see [the charts repository](https://github.com/ondat/charts/tree/main/charts/ondat-operator) for instructions.

&nbsp;

## Install Ondat on Kubernetes

### Install the storageos kubectl plugin

```
curl -sSLo kubectl-storageos.tar.gz \
    https://github.com/storageos/kubectl-storageos/releases/download/v1.1.0/kubectl-storageos_1.1.0_linux_amd64.tar.gz \
    && tar -xf kubectl-storageos.tar.gz \
    && chmod +x kubectl-storageos \
    && sudo mv kubectl-storageos /usr/local/bin/ \
    && rm kubectl-storageos.tar.gz
```

> 💡 You can find binaries for different architectures and systems in [kubectl
> plugin](https://github.com/storageos/kubectl-storageos/releases).

### Smoke test

```
kubectl storageos preflight
```

> 💡 This command will check that some of the core requirements are met for
> an Ondat deployment. It checks for CPU/memory, Kubernetes version
> and a valid container runtime.

> ⚠️ There are some requirements that the preflight checks are unable to
> test for - refer to [prerequisites](/docs/prerequisites) for a full list
> and be sure to verify that your infrastructure meets them.

### Option A: Install Ondat (embedded etcd, for development and testing)

```bash
kubectl storageos install \
    --include-etcd \
    --etcd-tls-enabled \
    --admin-username "myuser" \
    --admin-password "my-password"
```

> 💡 This is the easiest way to get up and running with Ondat, though
> do note that we currently recommend having an out-of-cluster etcd
> in production for maximum performance and stability.

> ⚠️ This requires a default `StorageClass` in the Kubernetes cluster.
> If the default isn't set, you may need to set up the [local-path](https://raw.githubusercontent.com/rancher/local-path-provisioner/master/deploy/local-path-storage.yaml)
> `StorageClass`. Note that this stores all data locally on the individual
> nodes and is not recommended for production installations.

### Option B: Install Ondat (bring-your-own etcd, for production)

```bash
kubectl storageos install \
    --etcd-endpoints 'storageos-etcd-client.storageos-etcd:2379' \
    --admin-username "myuser" \
    --admin-password "my-password"
```

> 💡 Define the etcd endpoints as a comma delimited list, e.g. 10.42.3.10:2379,10.42.1.8:2379,10.42.2.8:2379

> 💡 If the etcd endpoints are not defined, the plugin will prompt you and
> request the endpoints.

### Verify Ondat installation

Ondat installs all its components in the `storageos` namespace.

```bash
$ kubectl -n storageos get pod -w
NAME                                     READY   STATUS    RESTARTS   AGE
storageos-api-manager-65f5c9dbdf-59p2j   1/1     Running   0          36s
storageos-api-manager-65f5c9dbdf-nhxg2   1/1     Running   0          36s
storageos-csi-helper-65dc8ff9d8-ddsh9    3/3     Running   0          36s
storageos-node-4njd4                     3/3     Running   0          55s
storageos-node-5qnl7                     3/3     Running   0          56s
storageos-node-7xc4s                     3/3     Running   0          52s
storageos-node-bkzkx                     3/3     Running   0          58s
storageos-node-gwp52                     3/3     Running   0          62s
storageos-node-zqkk7                     3/3     Running   0          62s
storageos-operator-8f7c946f8-npj7l       2/2     Running   0          64s
storageos-scheduler-86b979c6df-wndj4     1/1     Running   0          64s
```

> Wait until all the pods are ready. It usually takes ~60 seconds to complete

### License cluster

> ⚠️ Newly installed Ondat clusters must be licensed within 24 hours. Our
> personal license is free, and supports up to 1TiB of provisioned storage.

To obtain a license, follow the instructions on our [licensing operations](/docs/operations/licensing) page.

## Airgapped clusters

Airgapped clusters can install Ondat by defining the container images uploaded
on private registries using the Custom Resource definition of the
StorageOSCluster. Check the kubectl plugin reference for the
[declarative installation](/docs/reference/kubectl-plugin#declarative-installation).

## First Ondat volume

If this is your first installation you may wish to follow the [Ondat Volume guide](/docs/operations/firstpvc) for an example of how
to mount an Ondat volume in a Pod.
