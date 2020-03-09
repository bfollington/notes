---
description: >-
  Resources for my thinking on UI Programming, Distributed Systems Design and
  Video Game Architecture. Spoilers: it's the same patterns everywhere.
---

# Software Architecture

* Event-Driven communication
  * Limit direct interaction between components of a system
* Data-Oriented
  * Events -&gt; Components -&gt; Data Updates
  * Data Updates -&gt; Components -&gt; Events
  * Requests -&gt; Components -&gt; Data Queries -&gt; Response
* Unidirectional, immutable, pure, side-effects at boundaries
  * [Functional core, imperative shell](https://app.gitbook.com/@bfollington/s/notes/~/drafts/-M1wtdl3z8PnDUBSFQlc/programming/functional-programming/functional-core-imperative-shell)
* Domain-Driven-Design
  * Carefully architect code around core business concepts

![My take on video-game architecture](../.gitbook/assets/arch.png)

When considering game architecture specifically a few extra elements become relevant:

* Zero-allocation event dispatch
  * Object pooling
* Minimal-allocation state update
  * Clever immutable data structures
* Minimal-allocation projection update
  * Infrequent projection changes can allocate
  * Frequent projection changes should be computed with no allocation
* Minimal-runtime-instantiation of game entities
  * Object pooling
* Object lifecycle handling, no dangling subscriptions

## Data-Oriented Architecture

{% embed url="https://blog.eyas.sh/2020/03/data-oriented-architecture/" %}

[https://engineering.fb.com/data-infrastructure/messenger/](https://engineering.fb.com/data-infrastructure/messenger/)

## Unidirectional Dataflow

{% embed url="https://guide.elm-lang.org/architecture/" %}

{% embed url="https://facebook.github.io/flux/docs/in-depth-overview" %}

{% embed url="https://cycle.js.org/" %}

## Event-Driven \(& Event-Sourcing, CQRS\)

{% embed url="https://blog.jacobsdata.com/2020/02/19/a-brief-intro-to-clean-architecture-clean-ddd-and-cqrs" %}

{% embed url="https://www.youtube.com/watch?v=US8QG9I1XW0&list=PLKbIl8vl5RyuzNnO3nCUCnbT3WqReNW36&index=3" %}

{% embed url="https://danielwhittaker.me/2020/02/20/cqrs-step-step-guide-flow-typical-application/" %}

{% embed url="https://www.youtube.com/watch?v=dI91AJVVuNg" %}

{% embed url="https://vimeo.com/131632601" %}

{% embed url="https://www.youtube.com/watch?v=I3uH3iiiDqY" %}

{% embed url="https://www.youtube.com/watch?v=STKCRSUsyP0" %}

{% embed url="https://github.com/kgrzybek/modular-monolith-with-ddd" %}



