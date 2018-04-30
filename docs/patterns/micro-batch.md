---
title: Micro-batching pattern
description: Optimize latency and throughput of a system by processing streaming data in small batches at frequent intervals. 
keywords: design pattern
author: alexbuckgit
ms.date: 05/01/2017

pnp.series.title: Cloud Design Patterns
pnp.pattern.categories: data-management, performance-scalability
---

# Micro-batching pattern

[!INCLUDE [header](../_includes/header.md)]

Enable a system to process streaming data in small batches at frequent intervals. This approach helps maximize system throughput while minimizing latency, and can reduce the number of processes required to achieve a desired level of throughput.

## Context and problem

Traditional data analysis techniques such as data warehouses rely on batch processes that run at scheduled intervals. These approaches typically have significant latency (the time required to fully process each piece of data). Today, analysis of large amounts of continuous incoming data requires a different strategy. 

Stream processing allows a system to handle a continuous and potentially unbounded stream of incoming events. Continuous stream processing handles one event at a time (sometimes referred to as a "tuple-at-a-time") as events are received. While this minimizes the latency in processing each event, it often results in suboptimal overall throughput for the system. This is due to the significant overhead of each round trip from the event source to the point of processing.  A preferable solution would increase system throughput without significant increasing latency.

## Solution

We can maximize system throughput by collecting incoming events as they arrive into small batches, and then periodically transmitting these batches for processing. This way, we only incur the cost of the round trip once per batch, rather than once per event. However, if the rate of incoming events is variable, waiting for a fixed number of events to arrive before transmitting a batch would cause an unacceptably high degree of latency in event processing. 

Instead, we can define thresholds for both a batch size and a batch interval. If either of these thresholds is reached, the batch is transmitted to the processing engine. This approach is known as micro-batching and effectively optimizes the system for low latency and high throughput. 

For example, we could configure an event source with a batch interval of 200ms and a batch size of 10 events. If only three events happen within the first 200ms interval, the event source will transmit a batch containing these three records. Then, if fifteen events are received in the next 100ms, the event source will tranmit these fifteen events in a batch (rather than waiting for the batch interval to complete). The event source will immediately start the next batch interval.

<!--
**TODO: Diagram**
![DIAGRAM DESCRIPTION](./_images/PATTERN-NAME-diagram.png)
-->

This solution has the following benefits:

<!--
- { Micro-batch systems can often achieve high throughput per node because they leverage the same optimizations as batch systems (e.g., vectorized processing), and do not incur any extra per-record overhead} {SparkDef}
- { Decrease overhead of processing each task - Server round trips + processing + response - Send 10 vs send 1 }
- { Fewer nodes for same data processing rate }
- { More suited for dynamic load balancing }
    - {Traditional record-at-a-time stream processing frameworks tend to partition streams unevenly to different nodes. Spark Streaming schedules tasks based on the availability of resources. {BigDataAnalytics}}
    - {Micro-batch systems can also use dynamic load balancing techniques to handle changing workloads (e.g., increasing or decreasing the number of tasks). The downside, however, is a higher base latency due to waiting to accumulate a micro-batch. In practice, the streaming applications that are large-scale enough to need to distribute their computation tend to prioritize throughput, so Spark has traditionally implemented micro-batch processing. {SparkDef}}
- { Processing-time delays? } {StreamingSystems}
- { Can be used with queues, pub-sub, etc. }
- { Processing in parallel {Flink}}
- micro-batch 
    Fewer nodes for same data processing rate
    More suited for dynamic load balancing
    Well-suited for latencies from 100ms to 1sec
        "For much lower latencies, you should consider a continuous processing system, or using a micro-batch system in conjunction with a fast serving layer to provide low-latency queries (e.g., outputting data into MySQL or Apache Cassandra, where it can be served to clients in milliseconds)."
-->

## Issues and considerations

<!-- 

{ Define a small batch size threshold to minimize latency }
{ Issues: Time window (Spark) vs.  # of events (Storm). We don’t need to mention a particular technology but we should discuss these two different approaches. Storm has a potential issue (mentioned in this link). Micro batching will incur additional latency because it waits for multiple events before processing it. How can we optimize latency? (with fast serving layer?) }

{Logical abstraction of the underlying execution environment, be it batch, micro-batch, or streaming, providing flexibility of choice in execution engine and avoiding system-level constructs (such as micro-batch size) from creeping into the logical API.}

{ Duplicate detection?}

{ Distinctions between ingest, processing, and storage? }

-->

Consider the following points when deciding how to implement this pattern:

- **Issue1** Here.

- **Issue2**. Here.

- **Issue3**. Here.

## When to use this pattern

Use this pattern when:

<!-->
{ You need to maximize throughput in streaming processing }

{ Used where volume of incoming tasks is variable }
-->

- A.
- B.
- C.

This pattern might not be useful when:

- The volume of incoming events is low enough that the overall system can handle it through normal per-request processing. In these cases, messaging or queuing patterns like Competing Consumers may be more effective.
- D.
- E.
- F.

## Example

{ Apache Spark example}

<!-- Implementations: 

    - Spark Streaming on HDInsight clusters
    - Apache Storm on HDInsight clusters
    - Azure Stream Analytics
    - Event Hubs
        - https://docs.microsoft.com/en-us/azure/event-hubs/event-hubs-dotnet-framework-getstarted-send
        - Event Hubs Dedicated
    - [Event Hubs Capture](https://docs.microsoft.com/en-us/azure/event-hubs/event-hubs-capture-overview)
    - Stream Analytics?
    
-->

## Related patterns and guidance

The following patterns and guidance might be relevant when implementing this pattern:

- 
- 
- 

<!-- Reference

- {MAA} [Mastering Azure Analytics](https://www.safaribooksonline.com/library/view/mastering-azure-analytics/9781491956649/) - Ch.5
- {BigData} [Big Data: Principles and best practices of scalable realtime data systems.](https://www.safaribooksonline.com/library/view/big-data-principles/9781617290343/kindle_split_000.html) - Ch.16
- {SparkDefinitive} [Spark: The Definitive Guide](https://www.safaribooksonline.com/library/view/spark-the-definitive/9781491912201/ch20.html#s5c0---stream-processing-fundamentals) - Ch.20
- {StreamingSystems} [Streaming Systems](https://www.safaribooksonline.com/library/view/streaming-systems/9781491983867/)

-->
