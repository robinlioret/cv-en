# Evaluate an idea with 6 pillars

The pillars are a method I use to filter out ideas by estimating roughly their ROI and they potential the pillars.
If the idea is not promising enough on at least on of them, it's immediately archived.


[//]: # (@formatter:off)
//// admonition | 
    type: tip
This method **does not aim to replace** any other one. It is a simple help for engineers who have a lot of ideas to 
filter them **before** talking about them with their manager, architect or clients (for the freelances ou there).

It aims to be applied at the very beginning of the though process. The internal dialog could look this:
/// tab | Dialog A
* This tech looks interesting.
* It can really benefit us on the productivity and cost optimization pillars.
* Let's talk about it to the architect.
///
/// tab | Dialog B
* This tech looks interesting.
* I would a tremendous amount of work for very little benefits on the stability pillar.
* I note the idea, it can be useful later, not for now.
///
////
[//]: # (@formatter:on)

A pillar is a main KPI of the computer system across multiple services. To this day, I identified 6 of them[^1].

[^1]: This method is still fresh and will probably evolve over time.

<figure markdown="span">
![pillars.svg](../_assets/images/pillars.svg)
</figure>

## Pillars in a nutshell

/// tab | Stability
Everything that reduces the number of incident, errors, uncontrolled burst of resource usage, etc.

Reduces the burden on the exploitation team so they can focus on business evolution tasks.

The stability of the application, alongside performance, is often directly in sight of the customers.
///

/// tab | Security
Everything that reduces the risk of data leak, successful attack, etc. Security is a wide topic on its own.

This pillar is ungrateful: security is a burden (additional work, costs, human time) until we need it.
///

/// tab | Performance
Everything that improves latency, resources consumption, provisioning reactivity, alerting reactivity, etc.

The performance of the application, alongside stability, is often directly in sight of the customers.
///

/// tab | Productivity
Everything that can reduces the time-to-market for the business projects, or automate tasks, or optimize organizational
processes (without sacrificing anything), etc.

In a more formal way: everything that reduces the human time needed to perform the same tasks.
///

/// tab | Cost optimization
Everything that reduces the overhaul cost of the computer system.
///

/// tab | Evolutivity
Everything that keeps the evolution possibilities opened. That pillar helps to support business evolution.

It takes root in the development principles : decoupling, abstraction, patterns...
///