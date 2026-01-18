### Links

- https://medium.com/@xuannhat25/a-sre-story-debugging-rabbitmq-connections-blocked-287a88bf1881
- https://www.topinterviewquestions.in/rabbitmq-interview-questions-and-answers-for-5-years-experience/

### Questions

How does RabbitMQ ensure message delivery?
Mechanisms:

- Publisher confirms
- Consumer acknowledgments
- Persistent messages & durable queues
- Clustering & DLX (Dead Letter Exchange)

### CLI

list of queues and messages inside them

```bash
rabbitmqctl list_queues
```

очистить очередь

```bash
rabbitmqctl purge_queue  DeliveryHook
```
