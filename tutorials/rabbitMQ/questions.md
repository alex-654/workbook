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

## CLI commands example

list of queues and messages inside them

```bash
rabbitmqctl list_queues
```

очистить очередь

```bash
rabbitmqctl purge_queue  DeliveryHook
```
