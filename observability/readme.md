# Observability

## Introduction

Observability is the set of capabilities of a system that allow to investigate whether it is performing and how. It provides the ability to understand the internal state of a system from the data it produces and the ability to explore that data to answer questions about what happened and why.

These capabilities enable proactive monitoring via ad-hoc queries and reporting, and reactive alerting. In addition, with the help of AI, they can assist in providing predictive information.

If the system departs from its normal operation, these capabilities ease the research into what is not going as expected. Telemetry information on the usage and performance of the system and its constituents assists in this research.

In some instances, observability is required to enable key scenarios (i.e. scaling out/up and in/down), where observations the current system status can be compared to the defined scaling thresholds.

## Concepts

### Components in the Observability Infrastructure

</br>

::: mermaid
flowchart LR;

E[Event Logging];
Ca(Capture);
Pp([Pre-processing/Aggregation]);
LO[[Local Output]];
Ps([Post-processing/Aggregation]);
C[(Collection)];
R[Reporting and Visualization]
AI((AI/ML));
S[/Streaming/];
PR[Pattern Recognition];

subgraph Observed System;
    Ca -.-> LO;
    Ca <-.-> Pp;
    Metrics --> Ca;
    E --> Ca;
end;

Ca -.-> S;
Ca ==> C;
C <-.-> Ps;
C --> R;
C --> Alerting;
Analysis --> Prediction;
Analysis --> PR;
AI -.-> Analysis;
C --> Analysis;

:::

### Events and Metrics

Events are incidents of interest that occur in the system. These can be domain specific, or tool or technology-specific, including exceptions.

Metrics are measurements of a particular process or activity considered over intervals of time.

#### Tracing

Tracing is the practice of tracking dependencies between logged events, and including enough context to enable engineers to follow the lifetime of a high-level transaction throught its execution in the system.

### Semantic Logging

Semantic log: [Good reading](https://github.com/Azure/diagnostics-eventflow)

* I now no longer will use ILogger style messages and instead use a more typed structured payload.
* How to structurally think about logging
  * Use semantic logging: Events are strongly typed for consistency/structure.
    * Rather than have logs just be atomic events - show where they are/how they relate.
    * Can think of logs as nodes in a b-trees, where their parents are the contexts they're called from and traversing vertically is a method stack.
    * In the end, all logs are serialized to strings on a file, but you can do more during runtime when they're objects.
* There are still no great semantic logging frameworks in place. Every framework a) requires message b) breaks into traces, events, metrics. Exceptions
  * OpenTelemetry does look promising in this space, but still has little ways to go.
  * AppInsights SDK can log to different solutions other than Azure AppInsights.
* Reframing how to think about observability to
  * What do you do when you can't run a debugger?
  * How do you gather enough information to figure out what happened retroactively?
  * Logs should not be human readable - we should use tools to parse through them.

## Logging Framework and other tools

Intro here - blah blah blah

### Observability platforms and products

The following table shows most notable players in Application Performance Monitoring and other Observability products at the time of writing.

| Name | Gartner rank| Notes |
| --- |:---: | :---: |
| AppDynamics | 1st Lead |
| Dynatrace   | 2nd Lead |
| New Relic   | 3rd Lead |
| Bradcom     | 4th Lead |
| Microsoft   | 1st Challenger |
| Splunk      | 1st visionary |
| Datadog     | 2nd visionary |
| Prometheus  | NA |
| Grafana     | NA |
| Graphite    | NA |

## Observability in ML

See the [Observability in ML guidance](./mlObservability.md)

## Guidance

There is [available guidance](./guidanceIndex.md) that you can read.