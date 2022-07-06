---
title: "Etcd"
---

Ondat requires an etcd cluster in order to function. For more information on
why etcd is required, see our [etcd concepts](/docs/concepts/etcd) page.

The etcd used by Kubernetes itself cannot be used for Ondat's configuration as per
standards set out by the Kubernetes project.

For most use-cases it is recommended to install the Ondat etcd operator, which
will manage creation and maintenance of Ondat's etcd cluster. In some
circumstances, it makes sense to install etcd on separate machines outside of
your Kubernetes cluster.
