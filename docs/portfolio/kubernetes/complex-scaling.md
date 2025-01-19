# Complex scaling

Classically, pod auto-scaling is computed from the pods' resource consumption like Memory or CPU, occasionally from the
number of HTTP request send to the service. This project was born from a uncanny request: scaling from multiple external
metrics.

We wanted the auto-scaling to be based on the database CPU usage and/or the number of messages in multiple event queues
and/or many other possibilities.

[//]: # (@formatter:off)
<div class="grid cards" markdown>
- ![aws.svg](../../_assets/logos/aws.svg){ style="height:25px"; align=left } AWS
- ![kubernetes.svg](../../_assets/logos/kubernetes.svg){ style="height:25px"; align=left } Kubernetes
- ![helm.svg](../../_assets/logos/helm.svg){ style="height:25px"; align=left } Helm
- ![keda.svg](../../_assets/logos/keda.svg){ style="height:25px"; align=left } Keda
</div>
[//]: # (@formatter:on)

## Need & Benefits

Many services consume databases resources, sometimes at the point where the database becomes unavailable leading to an
outage of the application. To optimize all of this, we wanted an auto-scaling method that can disable some asynchronous
services, scale them up and down based on a combination of metrics (both internal and external).

This kind of scaling is complex and very not a standard.

## My Roles & Missions

[//]: # (@formatter:off)
<div class="grid cards" markdown>
- <b>Lead</b><br/>I presented the project and taken it to its full potential.
- <b>Engineer</b><br/>I perform the realisation of the project.
</div>
[//]: # (@formatter:on)

## Progression

### POC

The first step was to create a POC proving that it is indeed possible in the given context. I was given 3 days.

[//]: # (@formatter:off)
/// admonition | My skills at the time
    type: info
At the beginning of this project, I was a beginner with Keda. I knew that Keda supports external metrics, but I had no
idea how it was working.

However, because of my experience in **procedural generation**, I was confident enough to know that if 
I could confirm Keda capabilities, I would be able to find the right formula.
///
[//]: # (@formatter:on)

I planned the POC in 3 steps:

1. **Explore Keda capabilities** and confirm that external metrics scaling combination was a possibility.
2. **Create a Helm chart version** to include this complex scaling to the current configuration, then deploy a dummy
   service to test it out.
3. **Prepare the restitution support** to present the POC results.

#### Explore Keda capabilities

Keda is very powerful, it indeed supports external metrics. The real question was, can they be combined into a custom
metric?

Well, yes and no. Keda provides Scaling Modifiers that enable engineers to combine metrics with a formula. I still need
to find the formula. At first, I just created a very simple one to validate the concept. It worked.

First step validated.

#### Create a Helm chart version

I published a new test version of a Helm chart, so I can validate the GitOps deployment. It worked without issues, Helm
is a powerful and flexible tool.

I played around with formulas and start to explore the formula syntax just to confirm that we could eventually find one
that fit our needs.

#### Prepare the restitution support

At this point, the technical part of the code were complete and all that remains was preparing the presentation. I was
determined to manage the complexity of the task and help non-technical persons to understand it.

I wrote a Python script to generate 3D graphs. These graphs helped a lot of designing the formula.

/// tab | Example

![complex-scaling-graph-1.png](../../_assets/images/complex-scaling-graph-1.png){ align=right }

Here is a graph representing the number of replicas depending on the max number of message in a set of queues and on the
database cpu utilization.

```
max(sqs_message_count_1, sqs_message_count_2) 
* max(0, 90 - cpu_rds_writer_percent) / 100
```

To summarize:

* As the database CPU usage increase, the service scale down no matter what.
* As the number of message in a queue increase, the service **tries** to scale up.
* Database load will always be the most important factor.

This is ideal for a low priority service.
///

After that, restitution arrived. The POC was validated.

### Implementation

After the POC, it was the time to rebuild from the ground up (1). I reworked the charts to include this feature and
prepare an extensive documentation for the other devops people to be autonomous. At this point, it was "only" designing
new formulas for other services.
{ .annotate }

1. A POC is, by essence, a temporary draft. Temporary things must not go to production.

Once the system in place, we onboarded more services and successfully achieved a smarter usage of the database
resources.