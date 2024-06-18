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

Declare queue with attributes before you use the queue.
- Attribute includes message TTL or queue length.

queue name
- 8 characters encoded with UTF. 255 bytes in total.
- name is assigned to queue when you declare them. Queue name is retruend in the response.

queue durability
- For a durable queue, its meta data is persisted in the disk.
- Publisher marks messages as persisted after messages are published to broker.
- Messages/Queues are survived after queue restarts.

binding
- **Exchange** uses binding to route messages to bound queues based on routing keys.
- A layer of indirection is on top of the queues.
    - Engineers doesn't have to define routing rules in their code.
- Messages are dropped or returned by **exchange** if messages can not be routed.
    - e.g. queues are not bound to a exchange

Consumers have two ways to consume messages from queues.
1. subscribe(push api), so messages are delivered to it.
    - Each queue can have multiple consumers or exclusive consumer.
2. pull(pull api), extra process in application to get the messages. It is not recommended due to inefficiency.

Each consumer has a consumer tag(identifier)
- Use the tag to unsubscribe the queue.

Messages are acknowledged in two modes
1. automatic model
    - Exchange acknowledged right after messages are delivered to the consumer.
    - e.g. basic.deliver, basic.get-ok
2. explicit model
    - Consumer acknowledged to the queue after consumers receive/persist/process messages.
    - Make sure persist/process operations is idempotent or the state of the data is immutable.
    - e.g. basic.ack

Requeue the messages or discard the messages when application can't process the message at specific app state.
- Be aware of the app state that may create infinite messages delivery loop.
- e.g. basic.reject

App can acknowledge multiple messagse but can't reject multiple messages.
- To do so, use **nack**

Prefetch the messages. Multiple messages are pushed to the consumer before consumer acknowledges.
- Improve throughput by saving round trip
- Load balance. Some consumers may be slow. By setting the prefetch limit, busy consumer won't receive next batch of messages.

messages
- header
    - Some fields used are by consumer.
        - Content type: serializing method like json, protocol buffer...etc
    - Some fields used by broker.
- payload
    - Broker won't inspect it.

AMQP 0-9-1 Methods are operations that broker/client can do. They are grouped logically.
- e.g. all methods related to exchange
```
exchange.declare
exchange.declare-ok
exchange.delete
exchange.delete-ok
```
