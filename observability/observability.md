# Intro to Observability (concepts/vocab)

* What is observability?
* How can achieve Observability?
* Why I do need an observability ? What kind of problems does it resolve and where does it help me ?
* What's the difference from AMP (Application Performance Monitoring) and Observability.
* Clarify the definitions of common topics (activity, span, trace, log, event, metric, etc)
* As an engineer, what kinds of observability exists and what are their use cases (Samples)?
* What is the difference between the observability of 'distributed tracing' (multiple service) and internal tracing (single service)
* Can I go to this as a newbie and get pointers to learn?

## Guidance

* General Guidance
* What's the best north start to follow when I am in a crossroad?
* How integration with observability capabilities affect on my code performance ? 
* Does integration with observabilities tools impacts my app security overall ?
* What are the big no-no's on Observability.
* Hidden caveats/limitations/looses  after observability integration ?

### Planning for Observability

* What's the minimal bar that I should meet on observability.
* What are some common patterns that I can use?  
* I need roughly estimate Dev & SRE efforts to dev/integrate/debug/support observability in my app ?
* Where do we look to get started given a specific scenario?
* How do I keep my code clean and flexible but still observable?

### Implementation Guidance and SDKs

* I've head Trace.WriteLine is an observability code smell. How can I identify and address?
* Why and when to use TelemetryClient, Ilogger, IDomainLogger, etc
* Should we use ILogger anymore?
* Recommendations  on the dimensions (semantic logging)
* How, when and why should an observability SDK be usedgit
* How do I stay stack agnostic (easily target Splunk, Application Insights, Prometheus, etc) (Matt's sample)
* Do you have a good sample code for basic observability?

## Tools and Frameworks

* What are the existing frameworks and products and how can I select the best for me?
* What technologies or products should I know about before going to customer?
* What are the CSE best practices for Applications Insights
* In terms of library to use, I have been seeing that OpenTelemetry can be used with a variety of Dashboards, so it worth to suggest something in this sense 
