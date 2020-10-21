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
* [Common Pitfalls](#Commom-Pitfalls)
* [Event Logging](#Event-Logging)
* [Metrics](#Metrics)
* [Tracing](#Tracing)
  * [Correlation Ids](#Correlation-IDs)
* [Alerting](#Alerting)

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


## Metrics

### Overview

Metrics provide a near real-time stream of data, informing operators and stakeholders about the functions the system is performing as well as its health. Unlike logging and tracing, metric data tends to be more efficient to transmit and store.

### Collection Methods

Metric collection approaches fall into two broad categories: push metrics & pull metrics. Push metrics means that the originating component sends the data to a remote service or agent. [Azure Monitor](https://azure.microsoft.com/en-us/services/monitor) and [Etsy's statsd](https://github.com/statsd/statsd) are examples of push metrics. Some of the strengths with push metrics include:

* Only require network egress to the remote target.
* Originating component controls the frequency of measurement.
* Simplified configuration as the component only needs to know the destination of where to send data.

Some of the trade-offs with this approach:

* At scale, it is much more difficult to control data transmission rates, which can cause service throttling or dropping of values.
* Determining if every component, particularly in a dynamic scale environment, is healthy and sending data is difficult.

In the case of pull metrics, each originating component publishes an endpoint for the metric agent to connect to and gather measurements. [Prometheus](https://prometheus.io/) and its ecosystem of tools are an example of pull style metrics. Benefits experienced using a pull metrics setup may involve:

* Singular configuration for determining what is measured and the frequency of measurement for the local environment.
* Every measurement target has a meta metric related to if the collection is successful or not, which can be used as a general health check.
* Support for routing, filtering and processing of metrics before sending them onto a globally central metrics store.

Items of concern to some may include:

* Configuring & managing data sources can lead to a complex configuration. Prometheus has tooling to auto-discover and configure data sources in some environments, such as Kubernetes, but there are always exceptions to this, which lead to configuration complexity.
* Network configuration may add further complexity if firewalls and other ACLs need to be managed to allow connectivity.

### Best Practices

#### When should I use metrics instead of logs?

[Logs vs Metrics](../readme.md) covers some high level guidance on when to utilize metric data and when to use log data. Both have a valuable part to play in creating observable systems.

#### What should be tracked?

System critical measurements that relate to the application/machine health, which are usually excellent alert candidates. Work with your engineering and devops peers to identify the metrics, but they may include:

* CPU and memory utilisation.
* Request rate.
* Queue length.
* Unexpected exception count.

Important business-related measurements, which drive reporting to stakeholders. Consult with the various stakeholders of the component, but some examples may include:

* Jobs performed.
* User Session length.
* Games played.
* Site visits.

#### Dimension Labels

Modern metric systems today usually define a single time series metric as the combination of the name of the metric and its dictionary of dimension labels. Labels are an excellent way to distinguish one instance of a metric, from another while still allowing for aggregation and other operations to be performed on the set for analysis. Some common labels used in metrics may include:

* Container Name
* Host name
* Code Version
* Kubernetes cluster name
* Azure Region

> Note: Since dimension labels are used for aggregations and grouping operations, do not use unique strings or those with high cardinality as the value of a label. The value of the label is significantly diminished for reporting and in many cases has a negative performance hit on the metric system used to track it.

### Recommended Tools

* [Azure Monitor](https://docs.microsoft.com/en-us/azure/azure-monitor/overview) - Umbrella of services including system metrics, log analytics and more.
* [Prometheus](https://docs.microsoft.com/en-us/azure/azure-monitor/overview) - A real-time monitoring & alerting application. It's exposition format for exposing time-series is the basis for OpenMetrics's standard format.
* [Thanos](https://thanos.io) - Open source, highly available Prometheus setup with long term storage capabilities.
* [Cortex](https://cortexmetrics.io) - Horizontally scalable, highly available, multi-tenant, long term Prometheus.
* [Grafana](https://grafana.com) - Open source dashboard & visualization tool. Supports Log, Metrics and Distributed tracing data sources.

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

## Alerting

One of the goals of building highly observable systems is to provide valuable insight into the behavior of the application. Observable systems allow problems to be identified and surfaced through alerts before end users are impacted.

### Best Practices

* The foremost thing to do before creating alerts is to implement observability. Without monitoring systems in place, it becomes next to impossible to know what activities need to be monitored and when to alert the teams.
* Identify what the application's minimum viable service quality needs to be. It is not what you intend to deliver, but is acceptable for the customer. These [Service Level Objectives](https://landing.google.com/sre/sre-book/chapters/*ervice-level-objectives/)(SLOs) are a metric for measurement of the application's performance.
* SLOs are defined with respect to the end users. The alerts must watch for visible impact to the user. For example, alerting on request rate, latency and errors.
* Use automated, scriptable tools to mimic end-to-end important code paths relatable to activities in the application. Create alert polices on user impacting events or metric rate of change.
* Alert fatigue is real. Engineers are recommended to pay attention to their monitoring system so that accurate alerts and thresholds can be defined.
* Establish a primary channel for alerts that needs immediate attention and tag the right team/person(s) based on the nature of the incident. Not every single alert needs to be sent to the primary on-call channel.
* Establish a secondary channel for items that need to be looked into and does not affect the users, yet. For example, storage that nearing capacity threshold. These items will be what the engineering services will look to regularly to monitor the *ealth of the system.
* It is important to learn from each incident and continually improve the process. After every incident has been triaged, conduct a [post mortem of the scenario](https://landing.google.com/sre/workbook/chapters/postmortem-culture/). Scenarios and situations that were not initially considered will occur and the post mortem workflow is a great way to highlight that to improve the monitoring/alerting of the system. Configuring an alert to detect that incident scenario is a good idea to see if the event occurs again.
