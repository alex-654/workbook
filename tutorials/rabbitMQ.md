Producing -> sending 
message publisher (sender)
A program that sends messages is a producer

A queue is the name for the post box in RabbitMQ
 Many producers can send messages that go to one queue, and many consumers can try to receive data from one queue


Consuming -> receiving.
Listens for messages
A consumer is a program that mostly waits to receive messages: 

An application can be both a producer and consumer

CLI
list of queues and messages inside them
```bash
rabbitmqctl list_queues
```


yii extension for rabbitMQ
config
	attempts
	ttr
	...
need serializer default is php serializer
have statuses STATUS_WAITING STATUS_RESERVED STATUS_DONE
sending message can be delayed
