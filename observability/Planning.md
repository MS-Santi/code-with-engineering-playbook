# Planning for Observability

During the planning phase all key desicion makers need to be included (Product Owner, Operations manager, etc)

Plan for monitoring carefully, start by including the monitoring service owner, the operations lead and other related personnel during the planning phase, and continue engaging them throughout the development and release cycle. Their focus will be to develop a monitoring configuration that's based on the following criteria:

* What is the composition of the service?
  * Are those dependencies monitored today?
  * If so, are there multiple tools involved?
  * Is there an opportunity to consolidate, without introducing risks?
* What is the SLA of the service?
  * How will I measure and report it?
* How healthy is defined in PKI's?
* What should the service dashboard look like when an incident is raised?
* What should the dashboard look like for the service owner, and for the team that supports the service?
* What metrics does the resource produce that we need to monitor?
* How will the service owner, support teams, and other personnel be searching and query the logs?

> How you answer those questions, and the criteria for alerting, determines how you'll use the monitoring platform.

If you're migrating from an existing monitoring platform or set of monitoring tools, use the migration as an opportunity to reevaluate the signals you collect.

This is especially true now that there are several cost factors to consider when you migrate or integrate with a cloud-based monitoring platform like Azure Monitor.

Remember, monitoring data needs to be actionable. You need to have optimized data collected to give you "a 10,000- foot view" of the overall health of the service.

The instrumentation that's defined to identify real incidents should be as simple, predictable, and reliable as possible. 
