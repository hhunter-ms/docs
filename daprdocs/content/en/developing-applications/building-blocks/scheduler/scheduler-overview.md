---
type: docs
title: "Scheduler overview"
linkTitle: "Overview"
weight: 1000
description: "Overview of the scheduler API building block"
---

The Scheduler API works as an orchestrator for scheduling jobs in the future,  either at a specific time or a specific interval. The scheduler helps you with scheduling jobs, including scalable actor reminders. 

The Scheduler consists of two parts that work together to seamlessly schedule jobs across all of Dapr's API building blocks:
- The Scheduler building block
- [The Scheduler control plane service]({{< ref "concepts/dapr-services/scheduler.md" >}})

<img src="/images/scheduler/scheduler-architecture.png" alt="Diagram showing basics of the Scheduler control plane and the Scheduler API">

## How it works

The Scheduler building block is a job orchestrator, not executor. The design guarantees *at least once* job execution with a bias towards durability and horizontal scaling over precision. This means:
- **Guaranteed:** A job is never invoked *before* the schedule is due.
- **Not guaranteed:** A ceiling time on when the job is invoked *after* the due time is reached.

All job details and user-associated data for scheduled jobs are stored in the embedded Etcd. 

## Features

### Delayed pub/sub

The Scheduler building block enables you to delay your pub/sub messaging. You can publish a message in a future specific time -- for example, a week from today, or a specific UTC date/time.

### Scheduled service invocation

The Scheduler building block provides the [service invocation]({{< ref service-invocation-overview.md >}}) building block with an orchestrator that schedules method calls between applications.

### Schedule jobs across multiple replicas

The Scheduler service enables the scheduling of jobs to scale across multiple replicas, while guaranteeing that a job is only triggered by 1 scheduler service instance.

### Scheduler reminders

Actors have actor reminders, but present some limitations involving scalability. Make reminders more scalable by using `SchedulerReminders`. 

## Try out the Scheduler

You can try out the Scheduler building block directly in your application. After [Dapr is installed]({{< ref install-dapr-cli.md >}}), you can begin using the Scheduler API, starting with [the How-to: Set up a Scheduler guide]({{< ref howto-use-scheduler.md >}}).

## Next steps

- [Learn how to use the scheduler in your environment]({{< ref howto-use-scheduler.md >}})
- [Learn more about the Scheduler control plane service]({{< ref "concepts/dapr-services/scheduler.md" >}})
- [Scheduler API reference]({{< ref scheduler_api.md >}})