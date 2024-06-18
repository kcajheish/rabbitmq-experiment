running rabbitmq in container
```code
# latest RabbitMQ 3.13
docker run -it --rm --name rabbitmq -p 5672:5672 rabbitmq:3.13-management
```

install external dependencies for Marven project
```
rabbitmq
SLF4J API
SLF4J Simple
```

Then rabbitmq server will listen at port 5672. Once you send a message, you see logs like below:
```
2024-06-16 18:02:27 2024-06-16 10:02:27.066324+00:00 [info] <0.867.0> accepting AMQP connection <0.867.0> (192.168.65.1:18080 -> 172.17.0.2:5672)
2024-06-16 18:02:27 2024-06-16 10:02:27.125494+00:00 [info] <0.867.0> connection <0.867.0> (192.168.65.1:18080 -> 172.17.0.2:5672): user 'guest' authenticated and granted access to vhost '/'
2024-06-16 18:02:27 2024-06-16 10:02:27.163232+00:00 [info] <0.867.0> closing AMQP connection <0.867.0> (192.168.65.1:18080 -> 172.17.0.2:5672, vhost: '/', user: 'guest')
```

[AMQP 0-9-1 Model](https://www.rabbitmq.com/tutorials/amqp-concepts)
- Publisher publishes messages to Exchange.
- Exchange routes message to the queue based on binding and exchange type.
- Consumer pulls the messages or rabbitmq pushes the message to the consumer.
- Messages are communicated over the network, so publisher and consumer can be in differnt machines.
- It's a programmable protocol. You setup queue with code. You don't have access to the server to setup those.
- Messages are removed from queue once Consumer send acknowledgement message to the broker.

Exchange attribute
- exchange type
- name
- durability
- auto delete
- arguments

default exchange
- Every queue is created and bind to default exchange if you don't specify the exchange type.
- Routing key is the queue name, which is determined when queue is created.
- Queue is binded to the exchange with the routing key. This means exchange routes message to the queue based on queue name.

direct exchange
- Publisher specify routing key in the messages.
- Exchange forwards that message to one/multiple queues based on the routing key.

fanout exchange
- Multiple queues are bound to a fanout exchange.
- Once a message publishes to that fanout exchange, that message will be published to all queues bound to that fanout exchange.

topic exchange
- Queues are bound to the exchange with a pattern. If message routing key matches the pattern, that message is routed to queues.

header exchange
- Queues are bound to exchange with attributes in header.
- Header exchange ignores routing key of the message and route them based on header.
- x-match specifies whether all attributes should match or not.

TBC
