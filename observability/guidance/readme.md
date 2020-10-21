# General Guidance

Questions for this guide:

* General Guidance
* What's the best north start to follow when I am in a crossroad?
* How integration with observability capabilities affect on my code performance ?
* Does integration with observabilities tools impacts my app security overall ?
* What are the big no-no's on Observability.
* Hidden caveats/limitations/looses  after observability integration ?

This document provides general guidance regarding observability.
Topics in this guide:

* [Getting Started](#Getting-Started---Recommended-Practices)
* [Common Pitfalls](#Common-Pitfalls)
* [Event Logging](./event-logging.md)
* [Metrics](./metrics.md)
* [Tracing](./tracing.md)
  * [Correlation Ids](./tracing.md#Correlation-IDs)
* [Alerting](./alerting.md)

More specific guidance is available for:

* [Planning Guidance](./planning.md)
* [Implementation Guidance](./implementation.md)

## Getting Started - Recommended Practices

1. **Correlation Id**: Include unique identifier at the start of the interaction to tie down aggregated data from various system components and provide a holistic view. Read more guidelines about using [correlation id](#Correlation-IDs).
2. Ensure health of the services are **monitored** and provide insights into system's performance and behavior.
3. **Faults, crashes, and failures** are logged as discrete events. This helps engineers identify problem area(s) during failures.
4. Ensure logging configuration (eg: setting logging to "verbose") can be controlled without code changes.
5. Ensure that **metrics** around latency and duration are collected and can be aggregated.
6. Start small and add where there is customer impact. [Avoiding metric fatigue](#metric-fatigue) is very crucial to collecting actionable data.
7. It is important that every data that is collected contains relevant and rich context.
8. Personally Identifiable Information or any other customer sensitive information should never be logged. Special attention should be paid to any local privacy data regulations and collected data must adhere to those. (ex: GPDR)

## Common Pitfalls

### Observability as an after thought

One of the design goals when building a system must be to have a monitorable system. This helps planning and thinking application availability, logging and metrics at the time of design and development. Observability also acts as a great debugging tool providing developers a bird's eye view of the system. By leaving instrumentation and logging of metrics towards the end, the development teams lose valuable insights during development.

### Metric Fatigue

1. It is recommended to collect and measure *what you need* **and** *not what you can*. Don't attempt to monitor everything.
1. If the data is not actionable, it is useless and becomes noise. On the contrary, it is sometimes very difficult to forecast every possible scenario that could go wrong.
1. There must be a balance between collecting what is needed vs. logging every single activity in the system. A general rule of thumb is to follow these principles:
    * rules that catch incidents must be simple, relevant and reliable
    * any data that is collected but not aggregated or alerted on must be reviewed if it is still required.

### Context

Every data logged must contain rich context, which is useful for getting an overall view of the system and easy to traceback errors/failures during troubleshooting. While logging data, care must also be taken to avoid data silos.

### Personally Identifiable Information

As a general rule, do not log any customer sensitive and Personal Identifiable Information (PII). Ensure any pertinent privacy regulations are followed regarding PII (Ex: GDPR etc.,)

## Tracing

### Overview

Produces the information required to observe series of correlated operations in a distributed system. Once collected they show the path, measurements and faults in a end to end transaction.

### Best Practices

* Ensure that at least key business transactions are traced.
* Include in each trace necessary information to identify software releases (i.e. service name, version). This is important to correlate deployments and system degradation.
* Ensure dependencies are included in trace (databases, I/O).
* If costs are a concern use sampling, avoiding throwing away errors, unexpected behaviour and critical information.
* Don't reinvent the wheel, use existing tools to collect and analyse the data.
* Ensure personal identifiable information policies and restrictions are followed.

### Recommended Tools

* [Azure Monitor](https://docs.microsoft.com/en-us/azure/azure-monitor/overview) - Umbrella of services including system metrics, log analytics and more.
* [Jaeger Tracing](https://www.jaegertracing.io) - Open source, end-to-end distributed tracing.
* [Grafana](https://grafana.com) - Open source dashboard & visualization tool. Supports Log, Metrics and Distributed tracing data sources.

### Correlation IDs

#### The Need

In a distributed system architecture (microservice architecture), it is highly difficult to understand a single end to end customer transaction flow through the various components.

Here are some the general challenges -

* It becomes challenging to understand the end-to-end behavior of a client request entering the application.
* Aggregation: Consolidating logs from multiple components and making sense out of these logs is difficult, if not impossible.
* Cyclic dependencies on services, course of events and asynchronous requests are not easily deciphered.
* While troubleshooting a request, the diagnostic context of the logs are very important to get to the root of the problem.

#### Solution

A Correlation ID is a unique identifier that is added to the very first interaction (incoming request) to  identify the context and is passed to all components that are involved in the transaction flow. Correlation ID becomes the glue that binds the transaction together and helps to draw a overall picture of events.

>Note: Before implementing your own Correlation ID, investigate if your telemetry tool of choice provides a auto-generated Correlation ID and that it serves the purposes of your application. For instance, [Application Insights](https://docs.microsoft.com/en-us/azure/azure-monitor/app/auto-collect-dependencies) offers dependency auto-collection for some application frameworks

#### Recommended Practices

1. Assign each external request a Correlation ID that binds the message to a transaction.
2. The Correlation ID for a transaction must be assigned as early as you can.
3. Propagate Correlation ID to all downstream components/services.
4. All components/services of the transaction use this Correlation ID in their logs.
5. For a HTTP Request, Correlation ID is typically passed in the header.
6. Where possible also add it to an outgoing response.
7. Based on the use case, there can be additional correlation IDs that may be needed. For instance, tracking logs based on both Session ID and User ID may be required. While adding multiple correlation ID, remember to propagate them through the components.

#### Use Cases

##### Log Correlation

Log correlation is the ability to track disparate events through different parts of the application. Having a Correlation ID provides more context making it easy to build rules for reporting and analysis.

##### Secondary reporting/observer systems

Using Correlation ID helps secondary systems to correlate data without application context. Some examples - generating metrics based on tracing data, integrating runtime/system diagnostics etc. For example, feeding AppInsights data and correlating it to infrastructure issues.

##### Troubleshooting Errors

For troubleshooting an errors, Correlation ID is a great starting point to trace the workflow of a transaction.
