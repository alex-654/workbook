## RabbitMq Cluster

- A RabbitMQ cluster is a logical grouping of one or more nodes each sharing
  users, virtual hosts, queues, streams, exchanges, bindings, runtime parameters and other distributed state.
- After cluster formation, all nodes in a cluster are aware of other cluster members.
- Client applications can be aware or not be aware of the fact that there are multiple cluster nodes, and connect to any
  of them.
- In a cluster, nodes identify and contact each other using node names.
- a client is only connected to one node at a time.
- In case of a node failure, clients should be able to reconnect to a different node, recover their topology and
  continue operation.

Every queue and stream in RabbitMQ has a primary replica (in case of classic queues, it is the only replica).
That replica is called the leader.
All publishing operations on queues and streams go through the leader replica first and then are replicated to the
followers (secondary replicas).
This is necessary to guarantee FIFO ordering of messages.


## Queue and Per-Message TTL

- Quorum queues support both Queue TTL and message TTL
- When using any form of message TTL, the memory overhead increases by 2 bytes per message

## Priorities

- Quorum queues internally only support two priorities: high and normal.
- High priority messages will be favoured over normal priority messages at a ratio of 2:1, i.e. for every 2 high
  priority message the queue will deliver 1 normal priority message.

## Poison Message Handling

- Quorum queues keep track of the number of unsuccessful (re)delivery attempts
- and expose it in the "x-delivery-count" header that is included with any redelivered message.
- Starting with RabbitMQ 4.0, the delivery limit for quorum queues defaults to 20.
- Messages that are redelivered more times than the limit allows for will be either dropped or dead-lettered (if
  a DLX is configured).

Configuring a Dead Letter Exchange using a Policy
```bash
rabbitmqctl set_policy DLX ".*" '{"dead-letter-exchange":"my-dlx"}' --apply-to queues --priority 7
```

## Consumers

### Delivery Acknowledgement Timeout

- RabbitMQ enforces a timeout on consumer delivery acknowledgement.
- The default timeout value for RabbitMQ is 30 minutes.

# Confirms

- Consumer acknowledgements (communication with consumers)
- Publisher confirms cover publisher communication with RabbitMQ
- The two features, however, are entirely orthogonal and unaware of each other.

### Consumer acknowledgements

- auto (immediately after it is sent) "fire-and-forget".
  Only recommended for consumers that can process deliveries efficiently and at a steady rate.
- mannual
  ack, nack/reject (nack can mark myltiple message, reject cant't)

nack/reject have bool option - requeue.
If true message will be placed to its original position in its queue or closer to queue head.
if all consumers requeue because they cannot process a delivery due to a transient condition, they will create a
requeue/redelivery loop.
