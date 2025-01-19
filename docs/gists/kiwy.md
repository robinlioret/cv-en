# Kiwy

Kiwy was a project I submitted to an internal innovation program[^1]. Its primary goal is to significantly reduce
boilerplate work when provisioning infrastructure by developing infrastructure abstraction, reusability, and automation
of dependencies. In other words, it enables procedural generation for infrastructure and application deployment. Simply
put, Kiwy aims to create a fully managed infrastructure that you own and control completely.

[^1]: It was not retained, perhaps because it didn't include enough "AI" (LLM) to be considered "innovative" by
corporate standards in 2024...

[//]: # (@formatter:off)
/// admonition | A typical situation
    type: example
Imagine a company or team needing a fully set-up Kubernetes cluster with all the supporting features for deployment, 
security, and documentation (1). We would need infrastructure with subnets, gateways, nodes, etc.
{ .annotate }

1. ArgoCD, Kyverno, Karpenter, Crossplane... All the possibilities offered by the Kubernetes ecosystem.

With Kiwy, you simply put together a relatively small IaC project, configure the main providers (such as cloud 
providers), import the relevant modules, and run the project. Since everything is packaged, all best practices are 
already implemented and adapted to your needs.

In just a few hours, you’ll have an infrastructure that’s ready to go. That’s the goal of Kiwy.
///
[//]: # (@formatter:on)

[//]: # (@formatter:off)
<div class="grid cards" markdown>
- ![python.svg](../_assets/logos/python.svg){ style="height:25px"; align=left } Python
- ![jinja.svg](../_assets/logos/jinja.svg){ style="height:25px"; align=left } Jinja2
- ![git.svg](../_assets/logos/git.svg){ style="height:25px"; align=left } Git
- ![argo.svg](../_assets/logos/argo.svg){ style="height:25px"; align=left } ArgoCD
- ![pulumi.svg](../_assets/logos/pulumi.svg){ style="height:25px"; align=left } Pulumi
- ...
</div>
[//]: # (@formatter:on)

## Leverage

Kiwy achieves its power by leveraging several key principles, including:

### Abstraction

- Hiding concrete implementation details behind an interface: the "black box" principle.
- Enables fast multi-cloud support by simply switching abstract factory modules/packages.

### Reusability

- Making things generic yet customizable greatly reduces the need to "reinvent the wheel" every time.
- The work of one person benefits dozens of others.

### Completeness

- With the Kiwy IDK, packages include helpers to generate all the boilerplate work: documentation, monitoring &
  observability, naming conventions, etc.

### Pro-activity

- Quality work takes time, but companies often cut corners to meet deadlines. This can lead to poor monitoring,
  architecture, security, documentation, and more—creating technical debt.
- With Kiwy, this work is done upfront, drastically improving the overall quality, stability, security, and more.

## Technology Stack for IDK

Due to Kiwy’s versatile nature, it supports a wide range of technologies. As with any IaC tool, it must be flexible
enough to accommodate the necessary tech. It will be up to the community to create plugins and packages for additional
technologies.

### Infrastructure as Code: Pulumi

Pulumi, like Terraform, is a tool that turns code into infrastructure (e.g., virtual machines, Kubernetes clusters,
network components). The main difference is the programming language used. While Terraform is limited by the declarative
nature of HCL, Pulumi uses real programming languages (1), giving it the power of both Terraform and a programming
language. This allows for abstraction and advanced coding principles, which are key to achieving Kiwy's goals.

{ .annotate }

1. Go, Java, C#, TypeScript, and Python.

#### IaC Language: Python

Since Pulumi is available in Go, Java, C#, TypeScript, and Python, we had to choose one for the proof of concept. I
chose Python, as it’s the language I’m most proficient in. Go is also a solid option since Pulumi and many cloud tools
are written in Go.

## Documentation: MkDocs Material

Kiwy relies on automation, and the documentation should be automated as well. Documentation should be in plain text and
easily editable, then published through pipelines. MkDocs is perfect for this—enabling static website building from
Markdown files. MkDocs Material is an open-source superset of MkDocs that adds many additional features.

[^2]: This resume was built with MkDocs Material and published to GitHub Pages through a pipeline.

## Concepts

Now that we have the core technologies, principles, and goals, let's explore the key concepts introduced by Kiwy.

### Component

A component is the smallest unit in the Kiwy IDK. It’s a concrete implementation of
a [Component Interface](#component-interfaces).

The main difference between a Kiwy Component and a Pulumi component lies in the packaged elements. A component is
considered complete if it meets the following requirements:

#### Metadata

- Naming conventions
- Tagging & labeling conventions

#### Deploys Resources

- Core resources (e.g., servers, networks)
- Monitoring & observability resources
- Backup resources
- Security resources
- Reporting resources

#### Documentation Resources

**Public Documentation**

- Use cases
- Configuration
- Update & upgrade procedures
- Changelogs

**Exploitation Documentation**

- Architecture module
- Exploitation procedures
- Recurring tasks
- Incident procedure
- DRP
- Monitoring & observability
- Use cases

#### CI/CD

At the component level, before release, various tests must be performed to enforce quality.

### Component Interfaces

A component interface is the contract between the factory or user and the component itself. It defines the available
information and actions for a component, similar to a Kubernetes `apiVersion` and `kind`.

### Factory

This is a direct implementation of the Factory design pattern, encapsulating the creation of a component into a
function.

### Abstract Factory

An implementation of the Abstract Factory design pattern. It encapsulates the creation of multiple types of components
in one place.

Abstract factories also implement an interface. For example, a developer doesn’t need to know if a Kubernetes cluster is
deployed on AWS, Azure, GCP, or OpenShift. The additional layer of abstraction makes it easier to switch or expand to
another cloud provider.

## Conclusion

This is a brief overview of the Kiwy project. There’s much more to it (I have a full binder of details). The project was
promising and worth exploring. I hope to have the opportunity to work on it again in the future!
