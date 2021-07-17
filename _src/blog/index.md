{
    "template": "../inc/page.template.source",
    "subtitle": "Blog"
}

---
# Instrumentation v. log aggregation?

### 12 October 2016

On the [Go kit mailing list](https://groups.google.com/forum/#!forum/go-kit), Chris Ford recently asked

> I came across the instrumentation part of Go kit and I am wondering if there
> is any advantage over aggregating log events? e.g. the instrumentation
> examples are about response time, request count and these kind of metrics. You
> could simply log every request and count the request events in the log
> afterwards. Am I correct about this assumption or am I missing something?
> Because I do not see a use case that can't be realized with log aggregation.
> Is guess the performance and realtimeness is better with instrumentation, but
> besides that?

Great question, and one I see a lot. In theory Chris is correct: if you logged
the complete details of every request to a system like ELK, in a way that was
queryable, you could construct the same information (99th quantile latencies,
error rates, etc.) post-hoc using a sufficiently sophisticated query language.
The reason to use (and, spoiler alert, almost always _prefer_) instrumentation
is ultimately about complexity, maintainability, and fitness to purpose.

First of all, you want to consider plain data volume: a human- and
machine-readable log line for each request with enough information is going to
generate a lot of traffic. I can speak from firsthand experience to say that
this can quickly outpace _actual production traffic_ and completely saturate all
kinds of resources: your network, your log shippers, the CPUs on the VMs
themselves, the machines/cluster responsible for storing your logs and making
them queryable, etc. I'm happy to acknowledge that this seems counterintuitive,
and like pointless worrying when you're starting out; trust me that it becomes
problematic a lot sooner than you think.

Then there's complexity in terms of what you actually want to accomplish by
tracking this stuff. The whole point is observability: getting some view into
the inner workings of the distributed system you've built. That means processing
latencies, quantiles, error rates, top-K resource consumers, etc. as first-order
concepts in some sense, rather than the text output of a map/reduce process over
some log data. And, ideally, you want those things on a close-to-realtime view
of your infra, with granularity on the order of seconds. Again, all of this is
in theory computable from text or structured application logs, but not in a way
that is nearly as useful, and not in a way that allows that information to serve
well as the foundation for more sophisticated feedback systems, reports, and as
a tool for system operators.

Finally, there's costs within the service itself. This loops back to my first
point: spitting out a line of text to stdout or rsyslog or whatever for every
trackable thing is a lot more expensive (CPU) than how the good instrumentation
systems do things: Prometheus instrumentation, for example, involves just
updating a register in memory, and at thousands of QPS that absolutely adds up.
There's also a point about software engineering lurking in here: recording and
making available good instrumentation about how your service is doing is part of
the _contract_ of that service to its runtime environment. Keeping your 99th
quantile latencies below 10ms (for example) is part of your service's SLA, and
recording/emitting data to validate that SLA is as much a necessary thing to
test for as anything else in your business logic. All this is to say you want
reliable, purpose-built primitives for this kind of thing: it's very difficult
to define a contract for your log lines, or unit test them properly.

For more on the latest point, including many many good reasons for
investing in proper instrumentation, see
 [the Site Reliability Engineering book](http://shop.oreilly.com/product/0636920041528.do)
from O'Reilly. And for more on logging v. instrumentation generally, shout out to me & my
eponymous blog post [Logging v instrumentation](https://peter.bourgon.org/blog/2016/02/07/logging-v-instrumentation.html).

<br/>

# Welcome to the new Go kit website!

### 01 July 2016

Go kit started its life as just a talk at a small conference,
 and spent its first 18 months as a GitHub repo with lots of breaking changes.
(Sorry about that.)
Many design discussions took place, initially in RFCs, and then in GitHub issues,
 on the mailing list, and especially in the Slack channel.
While the code can capture the rationale and outcome of those discussions,
 it can't so easily capture the _context_: the conversations themselves, the alternatives,
 the pros and cons of all the decisions, the intended usage, the caveats.
And it's often these things that are the most valuable, especially to newcomers.

So, welcome to the new Go kit website!
The primary purpose will be to capture and document the context
 behind the decisions that shaped Go kit.
I'll record and keep supplementary information to help you
 understand and effectively apply Go kit in your organization.

Initially, I'll focus on building up [an extensive FAQ](/faq)
 based on common questions and conversation in Slack, the mailing list, etc.
But no resource has proved nearly as useful as the examples,
 so I've also ported [the examples and tutorials from the repo](/examples)
 and will be adding to them as time goes on.

Happy programming 🏌