# General Guidance

* Extensibility for logging
  * Either easy to pull in/use but not detailed, or harder to implement/must be tightly coupled with code but can be very detailed.
  * Easier ones such as Azure Monitor/App Insights where they by default only go over low-level system diagnostics or high-level API call statuses/exceptions hit.
  * Harder ones such as OpenTelemetry are frameworks that enable semantic logging.
  * EventSource still has limitation that make it difficult to use in an application setting. DiagnosticSource looks more promising, however still not part of the framework.
* Code Structure
  * Avoid strong dependency at all costs, easier to change the behavior and refactor.
  * Test your observability components.
    * Verifies you get the right data when observing.
    * Pushes for better code and testing in business logic services.
  * Implementations of logging
    * How can you get as much information as possible without bloating your code?
    * ex/ OpenTelemetry abstracts away the logger itself but bloats your code by needing you to explicitly create new Activities for each new context.
    * Passing in an instance logger vs using a static logger
  * Constructors new() and static logging are code smells that should be highlighted and removed refactored.
  * Observability is a cross-cutting concern.
  * Must be decided from the beginning, as it’s difficult to add after the fact.
  * Telemetry implementations
    * As part of business logic
      * Pro: Easy to implement
      * Con: Makes code bulkier and (maybe) confusing
    * As decorator
      * Every observable type may be created via Factor or DI.
      * Pro: No clutter of business logic with observability code
      * Con: May be difficult to implement considering lifetime, asynchronous requirements, etc.

## Usage Guidance

When to use metric or events to track a particular piece of telemetry can be summarized with the following points:

* Use metrics to track the occurrence of an event, counting of items, the time taken to perform an action or to report the current value of a resource (CPU, memory, etc.)
* Use logs to track detailed information about an event also monitored by a metric, particularly errors, warnings or other exceptional situations.

## Things to watch for when building Observable Systems

### Observability as an after thought

One of the design goals when building a system must be to have a monitorable system. This helps planning and thinking application availability, logging and metrics at the time of design and development. Observability also acts as a great debugging tool providing developers a bird's eye view of the system. By leaving instrumentation and logging of metrics towards the end, the development teams lose valuable insights during development.

### Metric Fatigue

* It is recommended to collect and measure *what you need* **and** *not what you can*. Don't attempt to monitor everything.
* If the data is not actionable, it is useless and becomes noise. On the contrary, it is sometimes very difficult to forecast every possible scenario that could go wrong.
* There must be a balance between collecting what is needed vs. logging every single activity in the system. A general rule of thumb is to follow these principles

  * rules that catch incidents must be simple, relevant and reliable
  * any data that is collected but not aggregated or alerted on must be reviewed if it is still required.

### Context

Every data logged must contain rich context, which is useful for getting an overall view of the system and easy to traceback errors/failures during troubleshooting. While logging data, care must also be taken to avoid data silos.

### Personally Identifiable Information

As a general rule, do not log any customer sensitive and Personal Identifiable Information (PII). Ensure any pertinent privacy regulations are followed regarding PII (Ex: GDPR etc.,)