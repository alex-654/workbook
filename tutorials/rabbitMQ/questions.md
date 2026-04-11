## Links

- https://medium.com/@xuannhat25/a-sre-story-debugging-rabbitmq-connections-blocked-287a88bf1881
- https://www.topinterviewquestions.in/rabbitmq-interview-questions-and-answers-for-5-years-experience/
- https://www.rabbitmq.com/docs/heartbeats
- https://www.rabbitmq.com/blog/2014/04/14/finding-bottlenecks-with-rabbitmq-3-3
- https://www.rabbitmq.com/blog/2020/05/04/quorum-queues-and-flow-control-the-concepts

## Questions

### How does RabbitMQ ensure message delivery?

Mechanisms:

- Publisher confirms
- Consumer acknowledgments
- Persistent messages & durable queues
- Clustering & DLX (Dead Letter Exchange)

### What is heartbeat?

Heartbeat is conf variable, default is 60 sec.
The heartbeat timeout value defines after what period of time the peer TCP connection should be considered unreachable (
down) by RabbitMQ and client libraries. This value is negotiated between the client and RabbitMQ server at the time of
connection. The client must be configured to request heartbeats.

Symptoms of Mismatched/Missed Heartbeats

- Missed server heartbeat: The client hasn't heard from the broker and closes the connection.
- Connection reset by peer / Broken pipe: The RabbitMQ server closed the connection because it didn't receive a
  heartbeat from the client.

TCP keepalives can also be used instead of heartbeats by configuring them to lower system-specific values. In that case
heartbeats can be deactivated. The main benefit of this approach is that all TCP connections on a machine will use
identical values regardless of the protocol and client library used.

### Flow Control

- Flow Control - mechanism applied by RabbitMQ nodes to publishing connections in order to avoid runaway memory usage
  growth.
- RabbitMQ will reduce the speed of connections which are publishing too quickly for queues to keep up. No configuration
  is required.
- in flow state can be - connection, channels, queues. It depends what is bottleneck.

FLow control mechanisms

- Credit based flow control
- Memory alarms (When memory alarms kick in, all publishers are blocked.)
- Publisher confirms
- Consumer acknowledgements and prefetch

#### Credit Based Flow Control

Rate limiting message ingress
It allows various actors within the system to protect themselves and apply back pressure when they cannot process
messages fast enough
If a channel wants to send a message to a queue, it needs credit.
The queue grants the channel some initial credit, and then after that, each message that the channel sends to the queue
requires a credit. The queue will periodically grant the channel more credit, when it in turn has been able to pass
messages onto the persistence layer. If the channel doesn’t have credit, it is blocked from sending messages to the
queue until the queue grants it more.
So it's targeted at only those connections, channels and queues that are having issues, leaving other parts of the system
unaffected.
this state is known as “flow”

### What is a Virtual Hosts?

Virtual hosts provide logical grouping and separation of resources.   
RabbitMQ is a multi-tenant system: connections, exchanges, queues, bindings, user permissions, policies and some other
things belong to virtual hosts, logical groups of entities.
It provides multi-tenancy and isolation for different applications or environments (e.g., production and staging) within
the same physical infrastructure.

- One Cluster -> Many Vhosts

### The Consumer Capacity Metric (consumer utilisation)

The definition of consumer utilisation is the proportion of time that a queue's consumers could take new messages.
If its utilisation is less than 100% then this implies that its consumers are sometimes not able to take messages.

```
Prefetch limit	Consumer utilisation
1	            14%
3	            25%
10	            46%
30	            70%
1000            74%
```

For example: you can increase Prefetch limit to utilize consumers more, but after 30 in this example it stops work.
