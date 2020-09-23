# Tracing

## Overview

Produces the information required to observe series of correlated operations in a distributed system. Once collected they show the path, measurements and faults in a end to end transaction.

## Correlation IDs

### The Need

In a distributed system architecture (microservice architecture), it is highly difficult to understand a single end to end customer transaction flow through the various components.

Here are some the general challenges -

* It becomes challenging to understand the end-to-end behavior of a client request entering the application.
* Aggregation: Consolidating logs from multiple components and making sense out of these logs is difficult, if not impossible.
* Cyclic dependencies on services, course of events and asynchronous requests are not easily deciphered.
* While troubleshooting a request, the diagnostic context of the logs are very important to get to the root of the problem.

### Solution

A Correlation ID is a unique identifier that is added to the very first interaction (incoming request) to  identify the context and is passed to all components that are involved in the transaction flow. Correlation ID becomes the glue that binds the transaction together and helps to draw a overall picture of events.

>Note: Before implementing your own Correlation ID, investigate if your telemetry tool of choice provides a auto-generated Correlation ID and that it serves the purposes of your application. For instance, [Application Insights](https://docs.microsoft.com/en-us/azure/azure-monitor/app/auto-collect-dependencies) offers dependency auto-collection for some application frameworks

### Use Cases

#### Log Correlation

Log correlation is the ability to track disparate events through different parts of the application. Having a Correlation ID provides more context making it easy to build rules for reporting and analysis.

#### Secondary reporting/observer systems

Using Correlation ID helps secondary systems to correlate data without application context. Some examples - generating metrics based on tracing data, integrating runtime/system diagnostics etc. For example, feeding AppInsights data and correlating it to infrastructure issues.

#### Troubleshooting Errors

For troubleshooting an errors, Correlation ID is a great starting point to trace the workflow of a transaction.

## Best Practices

* Ensure that at least key business transactions are traced.
* Include in each trace necessary information to identify software releases (i.e. service name, version). This is important to correlate deployments and system degradation.
* Ensure dependencies are included in trace (databases, I/O).
* If costs are a concern use sampling, avoiding throwing away errors, unexpected behaviour and critical information.
* Don't reinvent the wheel, use existing tools to collect and analyse the data.
* Ensure personal identifiable information policies and restrictions are followed.

### CorrelationId Recommended Practices

* Assign each external request a Correlation ID that binds the message to a transaction.
* The Correlation ID for a transaction must be assigned as early as you can.
* Propagate Correlation ID to all downstream components/services.
* All components/services of the transaction use this Correlation ID in their logs.
* For a HTTP Request, Correlation ID is typically passed in the header.
* Where possible also add it to an outgoing response.
* Based on the use case, there can be additional correlation IDs that may be needed. For instance, tracking logs based on both Session ID and User ID may be required. While adding multiple correlation ID, remember to propagate them through the components.

## Recommended Tools

* [Azure Monitor](https://docs.microsoft.com/en-us/azure/azure-monitor/overview) - Umbrella of services including system metrics, log analytics and more.
* [Jaeger Tracing](https://www.jaegertracing.io) - Open source, end-to-end distributed tracing.
* [Grafana](https://grafana.com) - Open source dashboard & visualization tool. Supports Log, Metrics and Distributed tracing data sources.
