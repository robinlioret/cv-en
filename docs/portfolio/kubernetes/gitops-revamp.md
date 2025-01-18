# GitOps Revamp

Complete rework of the previous GitOps implementation. This involves deploying new Kubernetes clusters for each
environment then perform the deployment of all the workload including production.

[//]: # (@formatter:off)
<div class="grid cards" markdown>
- ![kubernetes.svg](../../_assets/logos/kubernetes.svg){ style="height:25px"; align=left } Kubernetes
- ![argo.svg](../../_assets/logos/argo.svg){ style="height:25px"; align=left } ArgoCD
- ![aws.svg](../../_assets/logos/aws.svg){ style="height:25px"; align=left } AWS
- ![helm.svg](../../_assets/logos/helm.svg){ style="height:25px"; align=left } Helm
- ![azure-devops.svg](../../_assets/logos/azure-devops.svg){ style="height:25px"; align=left } Azure DevOps
- ![git.svg](../../_assets/logos/git.svg){ style="height:25px"; align=left } Git
</div>
[//]: # (@formatter:on)

[//]: # (@formatter:off)
/// admonition | Disclaimer
    type: info
This page is intended for technical people.
///
[//]: # (@formatter:on)

## Need & Benefits

The previous implementation of GitOps was flawed:

- Too permissive permissions
- All environments in the same repository, leading to unpractical change management and errors (modifying production by
  mistake)
- Complex code organization
- Helm charts embedded with the configuration code

## My Roles & Missions

[//]: # (@formatter:off)
<div class="grid cards" markdown>
- <b>Lead</b><br/>I presented the project and taken it to its full potential.
- <b>Engineer</b><br/>I perform the realisation of the project.
</div>
[//]: # (@formatter:on)

## Specification

I had to rethink the entire implementation. Here is a simplified overview of the code organization for one environment.

```mermaid
flowchart LR
    subgraph aws["<b>AWS</b>"]
        subgraph eks["<b>Kubernetes cluster</b"]
            argocd("ArgoCD")
        end
    end

    subgraph azdo["<b>Azure DevOps</b>"]
        subgraph charts["<b>Helm Charts</b>"]
            meta_workload("helm-argocd-meta-apps<br/><i>chart repository</i>")

            subgraph app_charts["<b>Application charts</b>"]
                app_chart_1("Application 1")
                app_chart_2("Application 2")
                app_chart_3("Application 3")
                app_chart_4("...")
                app_chart_1 ~~~ app_chart_2
                app_chart_3 ~~~ app_chart_4
            end
        end

        configuration("gitops-workload-{env}<br/><i>gitops configuration repository</i>")
        configuration -.->|refers to| app_charts
    end

    argocd -->|deploys| meta_workload
    argocd -->|syncs on| configuration
    argocd -->|deploys| app_charts
```

[//]: # (@formatter:off)
//// admonition |
    type: abstract
This shows one environment. Find after that a complete example for 3 environments (dev, staging and production)

/// details | Complete organization example
    type: example


```mermaid
flowchart LR
    subgraph aws["<b>AWS</b>"]
        subgraph eks_dev["<b>Kubernetes cluster - DEV</b"]
          argocd_dev("ArgoCD - DEV")
        end
        subgraph eks_stg["<b>Kubernetes cluster - STAGING</b"]
          argocd_stg("ArgoCD - STAGING")
        end
        subgraph eks_prd["<b>Kubernetes cluster - PROD</b"]
          argocd_prd("ArgoCD - PROD")
        end
    end

    subgraph azdo["<b>Azure DevOps</b>"]
        subgraph charts["<b>Helm Charts</b>"]
            meta_workload("helm-argocd-meta-apps<br/><i>chart repository</i>")

            subgraph app_charts["<b>Application charts</b>"]
                app_chart_1("Application 1")
                app_chart_2("Application 2")
                app_chart_3("Application 3")
                app_chart_4("...")
                app_chart_1 ~~~ app_chart_2
                app_chart_3 ~~~ app_chart_4
            end
        end

        configuration_dev("gitops-workload-dev<br/><i>gitops configuration repository</i>")
        configuration_stg("gitops-workload-staging<br/><i>gitops configuration repository</i>")
        configuration_prd("gitops-workload-prod<br/><i>gitops configuration repository</i>")
        configuration_dev -.->|refers to| app_charts
        configuration_stg -.->|refers to| app_charts
        configuration_prd -.->|refers to| app_charts
    end

  argocd_dev -->|deploys| meta_workload
  argocd_dev -->|syncs on| configuration_dev
  argocd_dev -->|deploys| app_charts
  argocd_stg -->|deploys| meta_workload
  argocd_stg -->|syncs on| configuration_stg
  argocd_stg -->|deploys| app_charts
  argocd_prd -->|deploys| meta_workload
  argocd_prd -->|syncs on| configuration_prd
  argocd_prd -->|deploys| app_charts
```
///
////
[//]: # (@formatter:on)

While everything were in the same Git repository in the previous iteration, I separated the repositories. This ensures a
more controllable change management.

### Key benefits

With such organization (which is a standard in the GitOps practices), we gain many advantage over the previous
iteration.

The main ones are:

[//]: # (@formatter:off)
<div class="grid cards" markdown>
- <b>Permission control</b><br/>It is easier to manage who can modify each environment.
- <b>Easy chart versioning and promotion</b><br/>We can use git tags and branches like any other software part.
- <b>Clear change history</b><br/>One history per element makes troubleshoot much more efficient.
- <b>Environment separation</b><br/>Provides better control to promote changes.
</div>
[//]: # (@formatter:on)

## Progression

After writing the specification. We validated the solution. It was the moment to perform actions and promote the big
change toward production.

I build new clusters for each environment.

* First, because it made easier a rollback in case of issue (I was thinking in production mode from the start)
* Then, because there were a lot of development going on at the time. This way was the best to avoid disturbing
  developers during this project. And to keep every one productivity to its maximum.

Once the new clusters were up and running, we tested the switch and rollback in lower environments. Making sure that
everything was okay in both direction. Once secured. We moved on to production with minimum downtime.

## Conclusion

This was a large project that demanded more than 2 months of preparation and planning (1). But it was certainly worthy
of our time: it reduced the risks, improve tracking and change control. We discovered an unexpected benefit: people
started to like this new organization. That was a pleasant bonus!
{ .annotate }

1. I left out much of the complexity inherent to the company context for confidentiality reasons.