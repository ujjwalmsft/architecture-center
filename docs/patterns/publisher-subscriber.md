---
title: Competing Consumers
description: Enable multiple consumers of a service to receive event notifications asynchronously via a messaging channel.
keywords: design pattern
author: alexbuckgit
ms.date: 04/02/2018

pnp.series.title: Cloud Design Patterns
pnp.pattern.categories: [messaging]
---


# Publisher-Subscriber pattern

[!INCLUDE [header](../_includes/header.md)]

Enable a system to send notifications to other interested applications, without the sending system needing to be aware of or directly interface with the receiving systems.

- # Context and problem

Cloud-based applications often need to provide information to other applications as events happen. Asynchronous point-to-point communication via individual messaging channels might be sufficient if there are only a few consumers with limited communication needs. However, this approach cannot scale effectively to a large number of consumers, some of which might be interested only in a subset of the information. Producing applications should not have to keep track of even be aware of consuming applications.

# Solution

Create a **publish-subscribe** message channel, which consists of one input channel for the producing application and one output channel per consuming application. The producing application packages each event into a message using a defined format known to the consumers. The producing application connects to the publish-subscribe channel as the **publisher**, and sends messages via the input channel. Each consuming application subscribes to the publish-subscribe channel, which creates an output channel per **subscriber**. When a message is received via the input channel, the message is copied to each interested subscriber's output channel, typically by intermediary process called a **broker** or a **bus**. The asychronous broadcast approach provides high scalability and resiliency, and allows the producing application to stay responsive while distributing large amounts of information to consumers.

TODO: Diagram

This solution has the following benefits:

    - Benefits (many are asynch brokered messaging in general)
        - *Improves reliability
        - *Improves scalability
        - *Decouples workloads
        - *Temporal decoupling
        - *Cross-platform
        - *Asynchronous workflows
        - Improves testability
    - Broadcast-based (vs. list-based / content-based)

# Issues and considerations

    - Subscriber enrollment / tracking

    - Bi-directional communication (request/reply)

    - Wildcard subscriptions

    - Filter rules

    - Topics -> subscriptions (competing consumer, temporal decoupling, load leveling, and load balancing) 

- {cc, amp} **Message ordering**. The order in which consumer service instances receive messages isn't guaranteed, and doesn't necessarily reflect the order in which the messages were created. Design the system to ensure that message processing is idempotent because this will help to eliminate any dependency on the order in which messages are handled. For more information, see [Idempotency Patterns](http://blog.jonathanoliver.com/idempotency-patterns/) on Jonathon Oliver’s blog.

- {amp} **Message priority**. Some solutions may require that messages are processed in a specific order. The [Priority Queue pattern](priority-queue.md) provides a mechanism for ensuring specific messages are delivered before others.

- {amp, cc} **Poison message detection and handling**. A malformed message, or a task that requires access to resources that aren't available, can cause a service instance to fail. The system should prevent such messages being returned to the queue. Instead, capture and store the details of these messages elsewhere so that they can be analyzed if necessary.

- 
    - *Repeated messages
    - *Message expiration
    - *Message scheduling 
    - *Idempotency for partially completed work? 
    - Transaction handling vs eventual consistency
    - Distinguish between events (action events, data point events) and messages (commands, work jobs, transfers of control)
        - https://azure.microsoft.com/en-us/blog/events-data-points-and-messages-choosing-the-right-azure-messaging-service-for-your-data/
    - Topic discovery?
 
# When to use this pattern

Use this pattern when:

- An application needs to broadcast information to a significant number of consumers.
- An application needs to communicate with one or more independently-developed consumer applications, which may use different platforms, programming languages, and communication protocols.
- An application can send information to consumers without requiring real-time responses from the consumers.
- The systems being integrated are designed to support an eventual consistency model for their data. 
- An application needs to communicate information to multiple consumers which may have different availability requirements or uptime schedules than the producing application.

This pattern might not be useful when:

- An application has only a few consumers who need significantly different information from the producing application.
- An application requires near real-time replies from consumers.

# Technology options / examples
    - [Choose between Azure services that deliver messages](/azure/event-grid/compare-messaging-services)
        - [Azure Event Grid](/azure/event-grid/overview). Reactive programming / event distribution (discrete)	/ react to status changes
        - [Azure Event Hubs](). Big data pipeline	/ Event streaming (series) / Telemetry and distributed data streaming
            - low latency
            - capable of receiving and processing millions of events per second
        - [Azure Service Bus](/azure/service-bus-messaging/service-bus-fundamentals-hybrid-solutions) via [Topics and subscriptions](/azure/service-bus-messaging/service-bus-queues-topics-subscriptions#topics-and-subscriptions). High-value enterprise messaging	/ Messages / Order processing and financial transactions
            - reliable asynchronous message delivery (enterprise messaging as a service) that requires polling
            - advanced messaging features like FIFO, batching/sessions, transactions, dead-lettering, temporal control, routing and filtering, and duplicate detection
      - [Implementing event-based communication between microservices]()
    - [Advanced Message Queuing Protocol (AQMP)](https://en.wikipedia.org/wiki/Advanced_Message_Queuing_Protocol)
    - [Message Queuing Telemetry Transport (MQTT)](https://en.wikipedia.org/wiki/MQTT)
    - [Azure IoT Hub](/azure/iot-hub/iot-hub-what-is-iot-hub)
    - [SQL Server Service Broker]
    - Azure Storage queues provide basic queuing capabilities, but no built-in publish-subscribe capabilities. Applications requiring publish-subscribe capabilities should use Azure Service Bus. For more information, see [Azure Storage queues and Azure Service Bus queues - compared and contrasted](https://docs.microsoft.com/en-us/azure/service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted).

## Related patterns and guidance

The following patterns and guidance might be relevant when implementing this pattern:

- [Asynchronous Messaging Primer][amp]. Message queues are an asynchronous communications mechanism. If a consumer service needs to send a reply to an application, it might be necessary to implement some form of response messaging. The Asynchronous Messaging Primer provides information on how to implement request/reply messaging using message queues.

- [Pipes and Filters Pattern](pipes-and-filters.md).

- [Priority Queue Pattern](priority-queue.md). 

- [Competing Consumers Pattern][cc].

- Other patterns:
    - Messaging (general)
        - Multiple apps
            - Built independently
            - Different platforms
            - Different languages
            - Different maintenance schedules / uptime requirements
            - Decouples providers from consumers
          - Asynchronous
          - Reliable
          - Scalable
          - Resilient
    - Observer pattern
        - One-to-many communication (subject -> observers)
        - "Broadcast" communication
        - Decouples subject from observers
        - Pub/sub adds an "event channel"
    - Message pattern
        - Package events as messages
    - Message Channel pattern
        -  
    - Request-Reply pattern
        - Enables bi-directional communication via two separate channels
        - 
    - Broker pattern
    - 


<!-- links -->

[amp]: https://msdn.microsoft.com/library/dn589781.aspx
[cc]: ./competing-consumers.md

<!--
QUESTIONS:
- General terms - "application" vs "system"
-->
