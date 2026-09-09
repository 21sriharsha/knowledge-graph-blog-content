---
title: Kubernetes Networking
topics: Kubernetes
tags: networking, cni, kubernetes
summary: Every pod gets a routable address, and the CNI plugin is what makes that promise true.
---

# Kubernetes Networking

Kubernetes makes one deceptively simple promise about the network: **every pod gets its own IP
address, and every pod can reach every other pod without NAT**. Almost everything else about
cluster networking follows from taking that promise seriously.

## The flat network model

The model has three rules:

1. Pods communicate with each other without network address translation.
2. Nodes communicate with pods without NAT.
3. The address a pod sees for itself is the address others use to reach it.

That third rule is the one people trip over. A process binding to what it believes is its own
address must be reachable at that same address from elsewhere in the cluster.

## Where CNI fits

Kubernetes does not implement any of this. It delegates to a plugin that satisfies the
Container Network Interface contract — Calico, Cilium, flannel and the rest. The kubelet calls the
plugin when a pod is created and expects an address back.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: example
spec:
  containers:
    - name: app
      image: nginx
```

Nothing in that manifest mentions networking. The address is allocated underneath it.

## Services are a separate concern

A Service is a stable name and address in front of a changing set of pods. It is not part of the
flat network model — it is a layer on top, usually implemented with iptables or eBPF rules on each
node.

Once you need retries, timeouts and mutual TLS between services, a Service is no longer enough.
That is the problem [[Service Mesh Fundamentals]] exists to solve.

## Further reading

Debugging what the flat network is actually doing usually means packet capture, which is covered in
[[Debugging Cluster Traffic]].
