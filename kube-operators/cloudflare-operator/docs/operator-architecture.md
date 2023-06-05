[Home](https://imagine-devops.site/) » [Posts](https://imagine-devops.site/posts/)

# Cloudflare Tunnel Operator: Architecture

A bird's eye view of the Cloudflare Tunnel Operator.

January 21, 2022 · 4 min · Imagine-DevOps Hosavalike | [Suggest Changes](https://github.com/imagine-devops/blog-comments/issues)

![](https://imagine-devops.site/posts/migration-compose-k8s/cloudflare-tunnel-operator-architecture/CloudflareOperatorBanner.png)

Table of Contents

-   [Quick Look](https://imagine-devops.site/posts/migration-compose-k8s/cloudflare-tunnel-operator-architecture//#quick-look)
-   [Initial Approach: Ingress Controller](https://imagine-devops.site/posts/migration-compose-k8s/cloudflare-tunnel-operator-architecture//#initial-approach-ingress-controller)
-   [Creating a Custom Resource](https://imagine-devops.site/posts/migration-compose-k8s/cloudflare-tunnel-operator-architecture//#creating-a-custom-resource)
-   [Moving from Ingress to Service Controller](https://imagine-devops.site/posts/migration-compose-k8s/cloudflare-tunnel-operator-architecture//#moving-from-ingress-to-service-controller)
-   [Final Operator Architecture](https://imagine-devops.site/posts/migration-compose-k8s/cloudflare-tunnel-operator-architecture//#final-operator-architecture)

## Quick Look[#](https://imagine-devops.site/posts/migration-compose-k8s/cloudflare-tunnel-operator-architecture//#quick-look)

Repository: [![GitHub Repo](https://img.shields.io/badge/GitHub-imagine-devops%2Fcloudflare--operator-brightgreen)](https://github.com/imagine-devops/cloudflare-operator)

Stats: [![GitHub issues](https://img.shields.io/github/issues/imagine-devops/cloudflare-operator)](https://github.com/imagine-devops/cloudflare-operator/issues) [![GitHub forks](https://img.shields.io/github/forks/imagine-devops/cloudflare-operator)](https://github.com/imagine-devops/cloudflare-operator/network) [![GitHub stars](https://img.shields.io/github/stars/imagine-devops/cloudflare-operator)](https://github.com/imagine-devops/cloudflare-operator/stargazers) [![GitHub license](https://img.shields.io/github/license/imagine-devops/cloudflare-operator?color=brightgreen)](https://github.com/imagine-devops/cloudflare-operator/blob/main/LICENSE)

## Initial Approach: Ingress Controller[#](https://imagine-devops.site/posts/migration-compose-k8s/cloudflare-tunnel-operator-architecture//#initial-approach-ingress-controller)

I started out with a simple goal in mind that expanded a lot as I proceeded to code it. I wanted to build an Ingress Controller that does just one thing. It should read the annotations from my Ingress Resources, look at the ConfigMap and the target domain and modify the ConfigMap to add the target domain to it. FYI, an Operator is a combination of a Custom Resource and a Controller for it. What I wanted to build was just a Controller for an existing resource: an Ingress. The ConfigMap and the actual `cloudflared` deployment was to be [manually deployed using the official insturctions](https://developers.cloudflare.com/cloudflare-one/tutorials/many-cfd-one-tunnel#deploy-cloudflared) .

![](https://imagine-devops.site/posts/migration-compose-k8s/cloudflare-tunnel-operator-architecture//InitialArch.png#center)

Initial Architecture

While thinking of how to clean up deleted resources, I found out that [finalizers](https://kubernetes.io/docs/concepts/overview/working-with-objects/finalizers/) are for that exact reason. I wanted to add this so that when an Ingress is deleted, I could go through the ConfigMap and delete the unwanted entries.

## Creating a Custom Resource[#](https://imagine-devops.site/posts/migration-compose-k8s/cloudflare-tunnel-operator-architecture//#creating-a-custom-resource)

When I was half way through creating the above, I remembered that I would still need to create tunnels and Secrets manually, configure and manage it by myself. Moreover, I also would need to add DNS entries in the Cloudflare Dashboard. Automating this would not be a huge amount of work since [Cloudflare’s APIs](https://api.cloudflare.com/) are pretty good. They even have a Go library, [`cloudflare-go`](https://github.com/cloudflare/cloudflare-go) for the API which I found out after manually implementing API calls, so, as of writing this, it stays as [an issue #7 (PRs welcome 😊)](https://github.com/imagine-devops/cloudflare-operator/issues/7) .

To automate the end to end flow of creating and managing tunnels, I started creating a Tunnel Custom Resource and Controller combo which is an Operator Pattern. The Tunnel Resource would take inputs for Cloudflare credentials and other details such as domain names and email addresses. Following best practices, the credentials would be provided as a Secret reference. The Controller would have some tasks to be done. The [`tunnel_controller.go`](https://github.com/imagine-devops/cloudflare-operator/blob/main/controllers/tunnel_controller.go) would:

-   Create a [tunnel using the API](https://api.cloudflare.com/#argo-tunnel-create-argo-tunnel)
-   Add finalizers and cleanup during deletion
-   Create a Secret to hold the tunnel credentials
-   Create and maintain a ConfigMap and Deployment for `cloudflared`
-   Update the Deployment if scale for the Tunnel changes (to have multiple instances of `cloudflared` for a tunnel)

## Moving from Ingress to Service Controller[#](https://imagine-devops.site/posts/migration-compose-k8s/cloudflare-tunnel-operator-architecture//#moving-from-ingress-to-service-controller)

While testing the project, I remembered that `cloudflared` can not only proxy HTTP/S traffic, but also [TCP](https://developers.cloudflare.com/cloudflare-one/applications/non-http) (and [soon UDP](https://blog.cloudflare.com/extending-cloudflares-zero-trust-platform-to-support-udp-and-internal-dns/) ) traffic too. Then I remembered I do not need an Ingress at all to have the app accessible from the tunnel. With one less resource to create, I modified the Controller to work on Services rather than Ingresses. Now, with just your app deployment and a Service to access it, your app can be securely tunnled to the internet. You can still add an Ingress on top (maybe throw in [external-dns](https://github.com/kubernetes-sigs/external-dns) ) for local access and it would still work. No extra configuration required.

Also, since I already was accessing Cloudflare APIs, it was a no brainer to automatically create and delete DNS records when the Service Controller is triggered. At this point, the [`service_controller.go`](https://github.com/imagine-devops/cloudflare-operator/blob/main/controllers/service_controller.go) does the following:

-   Modify the ConfigMap to include the new Service to be tunneled
-   Add a DNS entry on Cloudflare to route traffic to this tunnel
-   Restart the `cloudflared` Pods to make the configuration change take effect
-   Add a finalizer to delete the DNS entry once the Service is deleted

## Final Operator Architecture[#](https://imagine-devops.site/posts/migration-compose-k8s/cloudflare-tunnel-operator-architecture//#final-operator-architecture)

As of writing this, the architecture looks more like below. The operator includes a Tunnel Custom Resource Definition, a Tunnel and a Service Controller that handles the end to end flow of working with tunnels to expose a service to the internet. Adding even more benefits, due to this being part of Cloudflare Access Zero Trust solution, you can add rules on who should be able to access the services through the [Cloudflare for Teams](https://dash.teams.cloudflare.com/) dashboard. An incredible value add for free!

![](https://imagine-devops.site/posts/migration-compose-k8s/cloudflare-tunnel-operator-architecture//OperatorArchitecture.png#center)

Final Operator Architecture

![Cloudflare Operator Logo](https://imagine-devops.site/posts/migration-compose-k8s/cloudflare-tunnel-operator-architecture//CloudflareOperatorLogo.png#center) [![GitHub Repo](https://img.shields.io/badge/GitHub-imagine-devops%2Fcloudflare--operator-brightgreen#center)](https://github.com/imagine-devops/cloudflare-operator)

Feel free to look at [this project over at Github](https://github.com/imagine-devops/cloudflare-operator) and let me know your thoughts below on the exciting usecases can you come up with!