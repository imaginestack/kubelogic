# kubelogic
Empower your Kubernetes deployment with kubelogic - a comprehensive repository of modules and playbooks for managing and provisioning highly available Kubernetes clusters, services, and resources on the edge. 

Whether you're a developer, IT administrator, or DevOps engineer, kubelogic offers a powerful and intuitive logic that simplifies the complexities of Kubernetes deployment. With tools like Ansible, Terraform, Metallb, KubeVIP, and more, kubelogic provides a complete solution for deploying and managing containerized workloads in modern computing environments. Take control of your Kubernetes deployment on the edge with kubelogic and unlock its full potential.

This repository contains a collection of scripts, modules, and playbooks for provisioning kubernetes clusters, and managing operators, monitoring and logging, security, backup and and recovey across multiple providers.


## 📖 Overview

Our repositories for our Kubernetes standard tools like [Ansible](https://www.ansible.com/), [Terraform](https://www.terraform.io/), [Kubernetes](https://kubernetes.io/), [Flux](https://github.com/fluxcd/flux2), [Renovate](https://github.com/renovatebot/renovate) and [GitHub Actions](https://github.com/features/actions).

  
## ⛵ Kubernetes

There is a template over at [imaginestudio-io/flux-cluster-template](https://github.com/imaginestudio-io/flux-cluster-template) if you wanted to try and follow along with some of the best practices utilized right here.

### Initial Installation

Our [k3s](https://k3s.io/) clusters are provisioned using the [Ansible](https://www.ansible.com/) galaxy role [ansible-role-k3s](https://github.com/PyratLabs/ansible-role-k3s). They are simultaneously provisioned with [kube-vip](https://kube-vip.chipzoller.dev/) to create a load balancer for control plane, and [metal-lb](https://metallb.universe.tf/installation/) for its service `LoadBalancer` overlayed on Ubuntu VM Server's. There are hyper-converged as well as semi-hyper converged clusters, workloads and block storage that sharing the differing resources on our internally and externally exposed nodes,backed with HA (NFS) file storage servers.

🔸 _[Click here](./provision/kubernetes/servers/) to see my Ansible playbooks and roles._

## Core Components

- [actions-runner-controller](https://github.com/actions/actions-runner-controller): Self-hosted Github runners.
- [calico](https://github.com/projectcalico/calico): Internal Kubernetes networking plugin.
- [traefik-proxy](https://doc.traefik.io/traefik/): Open-Source Edge Router that optimizes publishing your services.
- [metalLB](https://metallb.universe.tf/): Load Balancer implementation for Bare-Metal kubernetes services.
- [kube-vip](https://kube-vip.io/): Virtual IP and Load Balancer for HA control plane.
- [cert-manager](https://cert-manager.io/docs/): Creates SSL certificates for services in my Kubernetes cluster.
- [external-dns](https://github.com/kubernetes-sigs/external-dns): Automatically manages DNS records from my cluster in a cloud DNS provider.
- [external-secrets](
- [twingate](https://www.twingate.com/docs/): Zero-Trust Infrastructure access gateway.
- [ingress-nginx](https://github.com/kubernetes/ingress-nginx/): Ingress controller to expose HTTP traffic to pods over DNS.
- [rook](https://github.com/rook/rook): Distributed block storage for peristent storage.
- [sops](https://toolkit.fluxcd.io/guides/mozilla-sops/): Managed secrets for Kubernetes, Ansible and Terraform which are commited to Git.
- [tf-controller](https://github.com/weaveworks/tf-controller): Additional Flux component used to run Terraform from within a Kubernetes cluster.
- [volsync](https://github.com/backube/volsync) and [snapscheduler](https://github.com/backube/snapscheduler): Backup and recovery of persistent volume claims.

### GitOps

[Flux](https://github.com/fluxcd/flux2) watches my [kubernetes](./kubernetes/) folder (see Directories below) and makes the changes to my cluster based on the YAML manifests.

The way Flux works for me here is it will recursively search the [kubernetes/apps](./kubernetes/apps) folder until it finds the most top level `kustomization.yaml` per directory and then apply all the resources listed in it. That aforementioned `kustomization.yaml` will generally only have a namespace resource and one or many Flux kustomizations. Those Flux kustomizations will generally have a `HelmRelease` or other resources related to the application underneath it which will be applied.

[Renovate](https://github.com/renovatebot/renovate) watches my **entire** repository looking for dependency updates, when they are found a PR is automatically created. When some PRs are merged [Flux](https://github.com/fluxcd/flux2) applies the changes to my cluster.

### Directories

This Git repository contains the following directories under [kubernetes](./kubernetes/).

```sh
📁 kubernetes      # Kubernetes cluster defined as code
├─📁 bootstrap     # Flux installation
├─📁 flux          # Main Flux configuration of repository
└─📁 apps          # Apps deployed into my cluster grouped by namespace (see below)
```

### Cluster layout

Below is a a high level look at the layout of how my directory structure with Flux works. In this brief example you are able to see that `authelia` will not be able to run until `glauth` and  `cloudnative-pg` are running. It also shows that the `Cluster` custom resource depends on the `cloudnative-pg` Helm chart. This is needed because `cloudnative-pg` installs the `Cluster` custom resource definition in the Helm chart.

```python
# Key: <kind> :: <metadata.name>
GitRepository :: home-ops-kubernetes
    Kustomization :: cluster
        Kustomization :: cluster-apps
            Kustomization :: cluster-apps-authelia
                DependsOn:
                    Kustomization :: cluster-apps-glauth
                    Kustomization :: cluster-apps-cloudnative-pg-cluster
                HelmRelease :: authelia
            Kustomization :: cluster-apps-glauth
                HelmRelease :: glauth
            Kustomization :: cluster-apps-cloudnative-pg
                HelmRelease :: cloudnative-pg
            Kustomization :: cluster-apps-cloudnative-pg-cluster
                DependsOn:
                    Kustomization :: cluster-apps-cloudnative-pg
                Cluster :: postgres
```

### Networking

<details>
  <summary>Click to see a high level network diagram</summary>

  <img src="https://raw.githubusercontent.com/onedr0p/home-ops/main/docs/src/assets/networks.png" align="center" width="600px" alt="dns"/>
</details>


| Name                                          | CIDR              |
|-----------------------------------------------|-------------------|
| Management VLAN                               | `192.168.1.0/24`  |
| Kubernetes Nodes VLAN                         | `192.168.42.0/24` |
| Kubernetes external services (Calico w/ BGP)  | `192.168.69.0/24` |
| Kubernetes pods (Calico w/ BGP)               | `10.42.0.0/16`    |
| Kubernetes services (Calico w/ BGP)           | `10.43.0.0/16`    |

- HAProxy is configured on my `VyOS` router for the Kubernetes Control Plane Load Balancer.
- Calico is configured with `externalIPs` to expose Kubernetes services with their own IP over BGP which is configured on my router.

---

## ☁️ Multi-Cloud Integrated Deployment Options

While most of my infrastructure and workloads are selfhosted I do rely upon the cloud for certain key parts of my setup. This saves me from having to worry about two things. (1) Dealing with chicken/egg scenarios and (2) services I critically need whether my cluster is online or not.

The alternative solution to these two problems would be to host a Kubernetes cluster in the cloud and deploy applications like [HCVault](https://www.vaultproject.io/), [Vaultwarden](https://github.com/dani-garcia/vaultwarden), [ntfy](https://ntfy.sh/), and [Gatus](https://gatus.io/). However, maintaining another cluster and monitoring another group of workloads is a lot more time and effort than I am willing to put in.

| Service                                         | Use                                                               |           |
|-------------------------------------------------|-------------------------------------------------------------------|----------------|
| [1Password](https://1password.com/)             | Secrets with [External Secrets](https://external-secrets.io/)     |       |
| [B2 Storage](https://www.backblaze.com/b2)      | Offsite application backups                                       |        |
| [Cloudflare](https://www.cloudflare.com/)       | Domain, DNS and proxy management                                  |       |
| [Fastmail](https://fastmail.com/)               | Email hosting                                                     |      |
| [GCP](https://cloud.google.com/)                | Voice interactions with Home Assistant over Google Assistant      | Free           |
| [GitHub](https://github.com/)                   | Hosting this repository and continuous integration/deployments    | Free           |
| [Newsgroup Ninja](https://www.newsgroup.ninja/) | Usenet access                                                     |         |
| [NextDNS](https://nextdns.io/)                  | My routers DNS server which includes AdBlocking                   | ~20/yr         |
| [Pushover](https://pushover.net/)               | Kubernetes Alerts and application notifications                   | Free           |
| [Terraform Cloud](https://www.terraform.io/)    | Storing Terraform state                                           | Free           |

---

## 🌐 DNS

<details>
  <summary>Click to see a diagram of how I conquer DNS!</summary>

  <img src="https://raw.githubusercontent.com/onedr0p/home-ops/main/docs/src/assets/dns.png" align="center" width="600px" alt="dns"/>
</details>

### Ingress Controller

Over WAN, I have port forwarded ports `80` and `443` to the load balancer IP of my ingress controller that's running in my Kubernetes cluster.

[Cloudflare](https://www.cloudflare.com/) works as a proxy to hide my homes WAN IP and also as a firewall. When not on my home network, all the traffic coming into my ingress controller on port `80` and `443` comes from Cloudflare. In `VyOS` I block all IPs not originating from the [Cloudflares list of IP ranges](https://www.cloudflare.com/ips/).

### Internal DNS

[CoreDNS](https://github.com/coredns/coredns) is deployed on my `VyOS` router and listening on `:53`. All DNS queries for _**my**_ domains are forwarded to [k8s_gateway](https://github.com/ori-edge/k8s_gateway) that is running in my cluster. With this setup `k8s_gateway` has direct access to my clusters ingresses and services and serves DNS for them in my internal network.

### Ad Blocking

The upstream server in CoreDNS is set to NextDNS which provides me with AdBlocking for all devices on my network.

### External DNS

[external-dns](https://github.com/kubernetes-sigs/external-dns) is deployed in my cluster and configure to sync DNS records to [Cloudflare](https://www.cloudflare.com/). The only ingresses `external-dns` looks at to gather DNS records to put in `Cloudflare` are ones that have an annotation of `external-dns.alpha.kubernetes.io/target`

🔸 _[Click here](./provision/kubernetes/cloudflare) to see how else I manage Cloudflare with Terraform._

---

## 🔧 Optional Configurations



## 🤝 Gratitude and Thanks

Thanks to all the people who donate their time to the [Kubernetes @Home](https://discord.gg/k8s-at-home) Discord community. A lot of inspiration for my cluster comes from the people that have shared their clusters using the [k8s-at-home](https://github.com/topics/k8s-at-home) GitHub topic. Be sure to check out the [Kubernetes @Home search](https://nanne.dev/k8s-at-home-search/) for ideas on how to deploy applications or get ideas on what you can deploy.

---

## 📜 Changelog

See my _awful_ [commit history](https://github.com/onedr0p/home-ops/commits/main)

---

## 🔏 License

See [LICENSE](./LICENSE)

[![main](https://github.com/devxp-tech/gitops/actions/workflows/main.yaml/badge.svg)](https://github.com/devxp-tech/gitops/actions/workflows/main.yaml)
[![Quality Gate Status](https://sonar.diegoluisi.eti.br/api/project_badges/measure?project=gitops&metric=alert_status&token=f2955563c1490d9562ba76db3c4a7c4afdc9efba)](https://sonar.diegoluisi.eti.br/dashboard?id=gitops)
[![App Status](https://argocd.diegoluisi.eti.br/api/badge?name=argo-cd&revision=true)](https://argocd.diegoluisi.eti.br/applications/argo-cd)
# ⚙️ GitOps

GitOps is a set of best practices where the entire code delivery process is controlled via Git, including infrastructure and application definition as code and automation to complete updates and rollbacks.

The core idea of GitOps is to have a git repository that contains declarative descriptions of the desired infrastructure in the production environment and has an automated process to make the production environment match the state described in that repository.

Now that we know the central idea, let's go back a little bit to the acronym GitOps and let's understand the role of git, the role of ops and how they meet.

The Key GitOps Principles:

- The entire system (infrastructure and applications) is described declaratively.
- The canonical desired system state is versioned in Git.
- Changes approved are automated and applied to the system.
- Software agents ensure correctness and alert on divergence.

### <img align="left" alt="Git" width="18px" src="https://cncf-branding.netlify.app/img/projects/argo/icon/color/argo-icon-color.svg" /> [Argo CD Autopilot](https://argocd-autopilot.readthedocs.io/en/stable/)

New users to GitOps and Argo CD are not often sure how they should structure their repos, add applications, promote apps across environments, and manage the Argo CD installation itself using GitOps.

### Example:

```bash
argocd-autopilot app create argo-events --app github.com/argoproj/argo-events/blob/master/manifests/namespace-install.yaml -p ops --wait-timeout 2m
```

### <img align="left" alt="Git" width="18px" src="https://cncf-branding.netlify.app/img/projects/argo/icon/color/argo-icon-color.svg" /> [Argo CD](https://argo-cd.readthedocs.io/en/stable/)

Argo CD automates the deployment of the desired application states in the specified target environments. Application deployments can track updates to branches, tags, or pinned to a specific version of manifests at a Git commit. See tracking strategies for additional details about the different tracking strategies available.

### <img align="left" alt="Git" width="18px" src="https://cncf-branding.netlify.app/img/projects/argo/icon/color/argo-icon-color.svg" /> [Argo Events](https://argoproj.github.io/argo-events/)

Argo Events is an event-driven workflow automation framework for Kubernetes which helps you trigger K8s objects, Argo Workflows, Serverless workloads, etc. on events from a variety of sources like webhooks, S3, schedules, messaging queues, gcp pubsub, sns, sqs, etc.

### <img align="left" alt="Git" width="18px" src="https://cncf-branding.netlify.app/img/projects/argo/icon/color/argo-icon-color.svg" /> [Argo Rollouts](https://argoproj.github.io/argo-rollouts/)

Argo Rollouts is a Kubernetes controller and set of CRDs which provide advanced deployment capabilities such as blue-green, canary, canary analysis, experimentation, and progressive delivery features to Kubernetes.

Argo Rollouts (optionally) integrates with ingress controllers and service meshes, leveraging their traffic shaping abilities to gradually shift traffic to the new version during an update. Additionally, Rollouts can query and interpret metrics from various providers to verify key KPIs and drive automated promotion or rollback during an update.

### <img align="left" alt="Git" width="18px" src="https://cncf-branding.netlify.app/img/projects/argo/icon/color/argo-icon-color.svg" /> [Argo Workflows](https://argoproj.github.io/argo-workflows/)

Argo Workflows is an open source container-native workflow engine for orchestrating parallel jobs on Kubernetes. Argo Workflows is implemented as a Kubernetes CRD (Custom Resource Definition).

### 🔩 Tooling


|      Tools       | Version | Deployed |
| :--------------: | :-----: | :------: |
| argocd-autopilot | v0.3.0  |    ✅    |
|      argocd      |  2.3.0  |    ✅    |
|   argo-events    |  1.5.5  |    ✅    |
|  argo-workflows  |  3.2.8  |    ✅    |
|  argo-rollouts   |  1.1.1  |    ✅    |
|    kustomize     |         |    ✅    |
|  sealed-secrets  | 0.17.3  |    ✅    |
|      istio       | 1.11.4  |    ✅    |
|    wordpress     |   5.9   |    ✅    |
|   cert-manager   |  1.6.1  |    ✅    |
|    prometheus    |    x    |    ✅    |
|     grafana      |    x    |    ✅    |
|  metrics-server  |  0.5.1  |    ✅    |

### 🔨 To Do


|     Issue     |   Description   | Status |
| :-----------: | :-------------: | :----: |
|   wordpress   |  Fix kustomize  |   ✅   |
| argo-worflows |    Fix Build    |   ❌   |
| yaml-validate | Fix CI Workflow |   ❌   |

### 🛠️ Required Tools

|      Tools       | Version |
| :--------------: | :-----: |
|     kubectl      | v1.22.3 |
| argocd-autopilot | v0.2.28 |

### 🌳 Project Structure

````bash
.
├── apps
│   ├── argo-rollouts
│   ├── backstage
│   ├── cert-manager
│   ├── crossplane
│   ├── grafana
│   ├── istio-base
│   ├── istio-ingress
│   ├── istiod
│   ├── jaeger
│   ├── kiali-operator
│   ├── kubernetes-dashboard
│   ├── loki
│   ├── metrics-server
│   ├── prometheus
│   ├── promtail
│   ├── rollout-demo
│   ├── sealed-secrets
│   ├── sonarqube
│   └── wordpress
├── bootstrap
│   ├── argo-cd
│   └── cluster-resources
├── infra
│   └── networking
├── projects
└── secrets
````

### 🖊️ Code of Conduct

See [CODE-OF-CONDUCT.md](.github/CODE-OF-CONDUCT.md).
### 🔒 Security

See [SECURITY.md](.github/SECURITY.md).

## ✨ Contributions

We ❤️ contributions big or small. [See our guide](contributing.md) on how to get started.

### Thanks to all our contributors!

<a href="https://github.com/devxp-tech/gitops/graphs/contributors">
  <img src="https://contrib.rocks/image?repo=devxp-tech/gitops" />
</a>


[![Awesome](https://awesome.re/badge.svg)](https://awesome.re)

A curated list for awesome GitOps resources inspired by [@sindresorhus' awesome](https://github.com/sindresorhus/awesome)

We follow this [code of conduct](CODE-OF-CONDUCT.md).

## What is GitOps? <!-- omit in toc -->

GitOps is a way to do Kubernetes cluster management and application delivery.
It works by using [Git](https://git-scm.com/) as a single source of truth for
[declarative infrastructure and applications](https://en.wikipedia.org/wiki/Infrastructure_as_code),
together with tools ensuring the _actual state_ of infrastructure and
applications converges towards the _desired state_ declared in Git. With Git at
the center of your delivery pipelines, developers can make pull requests to
accelerate and simplify application deployments and operations tasks to your
infrastructure or container-orchestration system (e.g. [Kubernetes](https://kubernetes.io/)).

<p align="center"><img src="img/gitops_conceptual_diagram.svg" alt="Conceptual diagram of GitOps-based infrastructure" width="800px" /></p>

## Why is GitOps awesome? <!-- omit in toc -->

It [increases developer productivity](https://www.weave.works/technologies/gitops/#key-benefits), [enhances developer experience](https://www.weave.works/technologies/gitops/#key-benefits), [improves stability](https://www.weave.works/technologies/gitops/#key-benefits), all while having [higher reliability](https://www.weave.works/technologies/gitops/#key-benefits), [higher consistency](https://www.weave.works/technologies/gitops/#key-benefits) and [stronger security guarantees](https://www.weave.works/technologies/gitops/#key-benefits).

Modern software development practices _assume_ support for reviewing changes, tracking history, comparing versions, and rolling back bad updates; GitOps applies the same tooling and engineering perspective to managing the systems that deliver direct business value to users and customers.

<!-- toc -->

- [Background](#background)
- [Tools](#tools)
- [Ancillary Tools](#ancillary-tools)
  - [Notifications](#notifications)
  - [Secrets](#secrets)
- [Tutorials](#tutorials)
- [Community](#community)

<!-- tocstop -->

## Background

- [Operations by pull request](https://www.weave.works/blog/gitops-operations-by-pull-request) - a blog entry about how GitOps came about at Weaveworks
- [GitOps.tech](https://www.gitops.tech/) - a summary of how GitOps works
- [GitOps Conversation Kit](https://gitops-community.github.io/kit/) - How to showcase GitOps awesomeness and convince all stakeholders to implement it
- [GitOps Working Group](https://github.com/gitops-working-group/gitops-working-group) - GitHub repo of GitOps working group under the CNCF App Delivery SIG.

## Tools

- [ArgoCD](https://github.com/argoproj/argo-cd) - Declarative continuous deployment for Kubernetes
- [Atlantis](https://www.runatlantis.io/) - Terraform pull request automation
- [Autoapply](https://github.com/autoapply/autoapply) - Automatically apply changes from a Git repository to a Kubernetes cluster
- [Carvel](https://carvel.dev) — Tool suite for building, packaging, and managing software on Kubernetes in a GitOps way
- [CloudBees Rollout](https://rollout.io/) - Feature Flag as-a-Service that leverages GitOps & Config-as-Code (commercial product from CloudBees)
- [Flux](https://github.com/fluxcd/flux2) - Open and extensible continuous delivery solution for Kubernetes. Powered by GitOps Toolkit
- [Helm Operator](https://github.com/fluxcd/helm-operator) - Automates Helm Chart releases in a GitOps manner
- [Flagger](https://github.com/weaveworks/flagger) - Progressive delivery Kubernetes operator (Canary, A/B testing and Blue/Green deployments automation)
- [Ignite](https://github.com/weaveworks/ignite) - A Virtual Machine manager with a container UX and built-in GitOps
- [Faros](https://github.com/pusher/faros) - CRD based GitOps controller
- [Jenkins X](https://jenkins-x.io/) - a CI/CD platform for Kubernetes that provides pipeline automation, built-in GitOps and preview environments
- [KubeStack](https://www.kubestack.com/) - GitOps framework using Terraform for Cloud Kubernetes distros (AKS, GKE, and EKS) with CI/CD examples for common tools
- [Sceptre](https://github.com/Sceptre/sceptre) - Sceptre is a tool to drive AWS CloudFormation as part of a CI/CD pipeline by using Hooks
- [Weave GitOps OSS](https://github.com/weaveworks/weave-gitops) - Weave GitOps is a simple open source developer platform for people who want cloud native applications, without needing Kubernetes expertise.
- [Weave GitOps Enterprise](https://www.weave.works/product/gitops-enterprise/) - Weave GitOps Enterprise is a continuous operations product that makes it easy to deploy and manage Kubernetes clusters and applications at scale in any environment. (commercial product from Weaveworks)
- [Werf](https://werf.io) - GitOps tool with advanced features to build images and deploy them to Kubernetes (integrates with any existing CI system)
- [PipeCD](https://pipecd.dev/) - Continuous Delivery for Declarative Kubernetes, Serverless and Infrastructure Applications
- [Grant.rs](https://github.com/duyet/grant.rs) - Manage Redshift/Postgres privileges in GitOps style

## Ancillary Tools

### Notifications

- [Fluxcloud](https://github.com/topfreegames/fluxcloud) - Slack notifications for Flux without Weave Cloud

### Secrets

- [argocd-vault-plugin](https://argocd-vault-plugin.readthedocs.io/en/stable/) - An ArgoCD plugin to retrieve secrets from Vault and inject them into Kubernetes resources
- [git-secret](https://github.com/sobolevn/git-secret) - A bash-tool to store your private data inside a git repository
- [Kamus](https://github.com/Soluto/kamus) - Zero-trust secret encryption/decryption solution for Kubernetes applications
- [Sealed Secrets](https://github.com/bitnami-labs/sealed-secrets) - One-way encrypted Secrets
- [SOPS](https://github.com/mozilla/sops) - Secrets OPerationS
- [Vault Secrets Operator](https://github.com/ricoberger/vault-secrets-operator) - Sync secrets from Vault with Kubernetes

## Tutorials

- [Managing Helm releases the GitOps way](https://github.com/fluxcd/flux2-kustomize-helm-example) - Flux and Helm Operator tutorial
- [Automating Istio canary deployments with GitOps](https://github.com/stefanprodan/gitops-istio) - Progressive Delivery tutorial with Flagger, Flux, Helm Operator and Istio
- [Managing a multi-tenant cluster with GitOps](https://github.com/fluxcd/flux2-multi-tenancy) - Flux and Kustomize tutorial
- [GitOps-style continuous delivery with Cloud Build](https://cloud.google.com/kubernetes-engine/docs/tutorials/gitops-cloud-build) - Google Cloud Build tutorial

## Community

- [Kubernetes slack](https://slack.kubernetes.io/) - #gitops channel for discussion of GitOps patterns and tooling
- [CNCF slack](https://slack.cncf.io/) - #flux channel for discussion of GitOps patterns and tooling
- [Weaveworks slack](https://slack.weave.works/) - multiple channels (including #flagger, #wksctl and others) to discuss Weaveworks GitOps products, give feedback, and talk about general approaches
