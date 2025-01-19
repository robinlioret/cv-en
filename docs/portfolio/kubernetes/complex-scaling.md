# Complex Scaling

Classically, pod auto-scaling is computed from the pods' resource consumption, such as Memory or CPU, and occasionally
from the number of HTTP requests sent to the service. This project emerged from an unusual request: scaling based on
multiple external metrics.

We wanted the auto-scaling to be driven by database CPU usage, the number of messages in multiple event queues, and
potentially many other factors.

[//]: # (@formatter:off)
<div class="grid cards" markdown>
- ![aws.svg](../../_assets/logos/aws.svg){ style="height:25px"; align=left } AWS
- ![kubernetes.svg](../../_assets/logos/kubernetes.svg){ style="height:25px"; align=left } Kubernetes
- ![helm.svg](../../_assets/logos/helm.svg){ style="height:25px"; align=left } Helm
- ![keda.svg](../../_assets/logos/keda.svg){ style="height:25px"; align=left } Keda
</div>
[//]: # (@formatter:on)

## Need & Benefits

Many services consume database resources, sometimes to the point where the database becomes unavailable, causing
application outages. To optimize this, we needed an auto-scaling method that could disable or scale up and down certain
asynchronous services based on a combination of internal and external metrics.

This type of scaling is complex and not standard.

## My Roles & Missions

[//]: # (@formatter:off)
<div class="grid cards" markdown>
- <b>Lead</b><br/>I presented the project and took it to its full potential.
- <b>Engineer</b><br/>I implemented the project.
</div>
[//]: # (@formatter:off)

## Progression

### POC

The first step was to create a Proof of Concept (POC) to confirm that it was indeed possible to implement the requested solution. I was given 3 days.

[//]: # (@formatter:off)
/// admonition | My skills at the time
    type: info
At the beginning of this project, I was a beginner with Keda. I knew that Keda supports external metrics, but I had no idea how it worked.

However, due to my experience with **procedural generation**, I was confident that if I could confirm Keda's capabilities, I would be able to devise the right formula.
///
[//]: # (@formatter:off)

I planned the POC in 3 steps:

1. **Explore Keda's capabilities** and confirm that combining external metrics for scaling was possible.
2. **Create a Helm chart version** to include this complex scaling configuration and deploy a dummy service to test it.
3. **Prepare the restitution support** to present the POC results.

#### Explore Keda’s Capabilities

Keda is very powerful and does support external metrics. The real question was: could we combine them into a custom metric?

The answer was both yes and no. Keda provides **Scaling Modifiers**, which enable engineers to combine metrics using formulas. I still had to find the right formula. Initially, I created a simple formula to validate the concept, and it worked.  
First step validated.

#### Create a Helm Chart Version

I published a new test version of a Helm chart to validate the GitOps deployment, and it worked without any issues. Helm is a powerful and flexible tool.

I experimented with formulas and explored the syntax to confirm that we could eventually find one that met our needs.

#### Prepare the Restitution Support

At this point, the technical part was complete, and the only remaining task was preparing the presentation. I was determined to manage the complexity and help non-technical people understand it.

I wrote a Python script to generate 3D graphs to help visualize the formulas. These graphs played a significant role in designing the final formula.

/// tab | Example

![complex-scaling-graph-1.png](../../_assets/images/complex-scaling-graph-1.png){ align=right }

This graph represents the number of replicas depending on the max number of messages in a set of queues and the database CPU utilization.

```
max(sqs_message_count_1, sqs_message_count_2) 
* max(0, 90 - cpu_rds_writer_percent) / 100
```

To summarize:

- As the database CPU usage increases, the service scales down, regardless of the other metrics.
- As the number of messages in a queue increases, the service **tries** to scale up.
- The database load remains the most important factor in scaling decisions.

This approach was ideal for a low-priority service.

///

After the presentation, the POC was validated.

### Implementation

Following the POC (1), the time came to rebuild from the ground up. I reworked the charts to include this feature and 
prepared extensive documentation to ensure that other DevOps engineers could work autonomously. At this point, the task 
became designing new formulas for other services.
{ .annotate }

1. A POC is, by definition, a temporary draft. Temporary things should not go to production.

Once the system was in place, we onboarded more services and successfully achieved smarter usage of database resources.
