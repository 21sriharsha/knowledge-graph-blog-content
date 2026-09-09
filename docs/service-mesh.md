---
title: Service Mesh Fundamentals
topics: Kubernetes
tags: networking, service-mesh, kubernetes
summary: A service mesh moves retries, timeouts and mTLS out of application code and into a sidecar.
---

# Service Mesh Fundamentals

A service mesh takes the concerns every distributed system eventually needs — retries, timeouts,
circuit breaking, mutual TLS, traffic shifting — and moves them out of application code into a
proxy that runs beside each service.

## Why not a library

The library approach works until you have more than one language. Then you maintain the same retry
semantics in Java, Go and Python, and they drift. The mesh trades that for a proxy per pod: one
implementation, configured declaratively, at the cost of a hop.

| Concern | In application code | In the mesh |
|---|---|---|
| Retries | per-language library | one proxy configuration |
| mTLS | certificate handling in every service | issued and rotated by the mesh |
| Traffic shifting | deploy-time | runtime, without redeploying |
| Observability | per-language instrumentation | uniform, from the proxy |

## The cost

The proxy is real. Every call now traverses two extra hops, and the control plane is another system
to operate and upgrade. On a small cluster the mesh often costs more than it returns.

It is worth adopting when you have enough services that the *drift* between their networking
behaviour is a bigger problem than the latency the proxy adds.

## Prerequisites

None of this replaces the underlying network. The mesh assumes the flat pod network described in
[[Kubernetes Networking]] already works — if pods cannot reach each other, a sidecar will not help.
