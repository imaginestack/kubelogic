<h1 align=center>Imagine Cloudflared Tunnel Operator</h1>

<div align="center">
  <a href="https://github.com/imagine-devops/cloudflare-operator">
    <img src="images/CloudflareOperatorLogo.png" alt="Logo" height="250">
  </a>
  <br />

  <p align="center">
    A Kubernetes Operator to create and manage Cloudflare Tunnels and DNS records <br /> for (HTTP/TCP/UDP*) Service Resources
    <br />
    <br />
    <a href="docs/getting-started.md"><strong>Getting Started Guide »</strong></a>
    <br />
    <br />
    <a href="https://github.com/imagine-devops/cloudflare-operator/issues">Report Bug</a>
    ·
    <a href="https://github.com/imagine-devops/cloudflare-operator/issues">Request Feature</a>
    <br />
  </p>
</div>



> **_NOTE_**: This project is currently in Alpha

> UDP*: UDP support for Cloudflare Tunnels is in [Early Access](https://blog.cloudflare.com/extending-cloudflares-zero-trust-platform-to-support-udp-and-internal-dns/)

## Motivation

The [Cloudflare Tunnels guide](https://developers.cloudflare.com/cloudflare-one/tutorials/many-cfd-one-tunnel) for deployment on Kubernetes provides a [manifest](https://github.com/cloudflare/argo-tunnel-examples/tree/master/named-tunnel-k8s) which is very bare bones and does not hook into Kubernetes in any meaningful way. The operator started out as a hobby project of mine to deploy applications in my home lab and expose them to the internet via Cloudflare Tunnels without doing a lot of manual work every time a new application is deployed.

## Overview

The Cloudflare Operator aims to provide a new way of dynamically deploying the [cloudflared](https://github.com/cloudflare/cloudflared) daemon on Kubernetes. Scaffolded and built using `operator-sdk`. Once deployed, this operator provides the following:

* Ability to create new and use existing Tunnels for [Cloudflare for Teams](https://developers.cloudflare.com/cloudflare-one/) using Custom Resources (CR/CRD) which will:
  * Accept a Secret for Cloudflare API Tokens and Keys
  * Run a scaled (configurable) Deployment of `cloudflared`
  * Manage a ConfigMap for the above Deployment
  * Have Cluster and Namespace scoped Tunnels
* A TunnelBinding controller which does the following:
  * Update the `cloudflared` ConfigMap to include the new Services to be served under a given Tunnel
  * Restart the `cloudflared` Deployment to make the configuration change take effect
  * Add a DNS entry in Cloudflare for the specified domain to be a [proxied CNAME to the referenced tunnel](https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/routing-to-tunnel/dns)
  * Reverse the above when the TunnelBinding is deleted using Finalizers

## Bird's eye view

Here is how the operator and the Tunnel Resource fit into your deployment.

![Operator Architecture](images/OperatorArchitecture.png#center)


## Getting Started

Go through the dedicated documentation on [Getting Started](docs/getting-started.md) to learn how to deploy this operator and a sample tunnel along with a service to expose.

Look into the [Configuration](docs/configuration.md) documentation to understand various configurable parameters of this operator.

> **_NOTE_**: This is **NOT** an official operator provided/backed by Cloudflare Inc. It utilizes their [v4 API](https://api.cloudflare.com/) and their [`cloudflared`](https://github.com/cloudflare/cloudflared) to automate setting up of tunnels on Kubernetes.
