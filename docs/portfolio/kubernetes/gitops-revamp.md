# GitOps Revamp

This project involved a complete rework of the previous GitOps implementation. It included deploying new Kubernetes
clusters for each environment and performing the deployment of all workloads, including production.

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
* This page is intended for technical people.
///
[//]: # (@formatter:off)

## Need & Benefits

The previous implementation of GitOps had several flaws:

- Too permissive permissions
- All environments in the same repository, which led to poor change management and the risk of accidental modifications (e.g., modifying production by mistake)
- Complex code organization
- Helm charts were embedded within the configuration code
- More flexible GitOps bridge (Terraform to ArgoCD)

## My Roles & Missions

[//]: # (@formatter:off)
<div class="grid cards" markdown>
- <b>Lead</b><br/>I presented the project and drove it to its full potential.
- <b>Engineer</b><br/>I implemented the project.
</div>
[//]: # (@formatter:off)

## Specification

I had to rethink the entire implementation. Below is a simplified overview of the code organization for one environment.

```mermaid
flowchart LR
    subgraph aws["<b>AWS</b>"]
        subgraph eks["<b>Kubernetes cluster</b>"]
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
This shows one environment. Find below a complete example for 3 environments (dev, staging, and production).

/// details | Complete organization example
    type: example

```mermaid
flowchart LR
    subgraph aws["<b>AWS</b>"]
        subgraph eks_dev["<b>Kubernetes cluster<br/>DEV</b>"]
          argocd_dev("ArgoCD<br/>DEV")
        end
        subgraph eks_stg["<b>Kubernetes cluster<br/>STAGING</b>"]
          argocd_stg("ArgoCD<br/>STAGING")
        end
        subgraph eks_prd["<b>Kubernetes cluster<br/>PROD</b>"]
          argocd_prd("ArgoCD<br/>PROD")
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

In the previous implementation, everything was in the same Git repository. I separated the repositories, ensuring more
controllable change management.

### Key Benefits

This new structure, which aligns with GitOps practices, provides several advantages over the previous iteration. The key
benefits are:

[//]: # (@formatter:off)
<div class="grid cards" markdown>
- <b>Permission control</b><br/>It is easier to manage who can modify each environment.
- <b>Easy chart versioning and promotion</b><br/>We can use Git tags and branches just like any other software component.
- <b>Clear change history</b><br/>One history per element makes troubleshooting much more efficient.
- <b>Environment separation</b><br/>Provides better control over promoting changes.
</div>
[//]: # (@formatter:off)

## Progression

After writing the specification and validating the solution, it was time to act and promote the significant change to production.

I built new clusters for each environment:

- **First**, because it made rolling back easier in case of an issue (I was thinking in production terms from the start).
- **Second**, because there was a lot of ongoing development. This approach prevented disturbing developers during the project, ensuring maximum productivity.

Once the new clusters were set up, we tested the switch and rollback in lower environments. We ensured everything worked in both directions. After securing this process, we moved on to production with minimal downtime.

## Conclusion

This was a large project that demanded over two months of preparation and planning (1). However, the results were worth 
it: it reduced risks, improved tracking, and gave better control over changes. An unexpected benefit was that people 
started appreciating the new organization—a pleasant bonus!
{ .annotate }

1. Due to confidentiality, I left out some of the complexity inherent to the company context.
