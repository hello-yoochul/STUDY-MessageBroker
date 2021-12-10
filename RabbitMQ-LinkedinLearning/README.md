[RabbitMQ website](https://www.rabbitmq.com/) 

# About course

RabbitMQ is one of the most popular open-source message brokers in use today. This lightweight software can help companies that have adopted a microservices model stitch together and enable communication between their services. In this course, join instructor Peter Morlion as he demonstrates how to properly install and work with RabbitMQ as a developer. After delving into the fundamental concepts behind message-based systems and the AMQP message protocol, Peter explains why you might want to use RabbitMQ, as well as how to install it. He then showcases how to implement the tool, covering both basic usage, such as how to publish to and consume from a RabbitMQ exchange, as well as more advanced topics like setting up and using authentication in RabbitMQ.

# Learning objectives

- How message-based systems are used
- The AMQP protocol
- Exchange type use cases
- Publishing to RabbitMQ
- Consuming from RabbitMQ
- Filtering messages with direct and topic exchanges
- Setting up and using authentication
- Authorizing and blocking application actions
- Tracking message contents for troubleshooting

# 1 Get to know RabbitMQ

- RabbitMQ message-based system 
  - RPC는 서로 URL이나 IP로 통신하는데, 수신자가 증가할 경우 감당이 불가.
  - 장점
    - 수신자 어딨는지 몰라도 된다
    - 다수 수신자 가능
    - queueing
    - 시스템 decoupling 가능
  - 단점
    - 비동기 통신: 수신을 바로 못할수도
    - 브로커가 망가지면, 통신 안됨 (클러스터링으로 해결가능)
    - 많은 네트워크 통신
- The AMQP protocol (Advanced Message Queuing Protocol)
  - RabbitMQ 프로토콜 지원 종류 
    - AMQP 0-9-1
    - [STOMP](https://stomp.github.io/)
    - [MQTT](https://mqtt.org/)
    - AMQP 1.0
  - AMQP 
    - Durability가 있지만 persist 하진 않는다. 시스템 restart시 다 날라감. 
    - auto delete 설정 
    - ![](https://support.smartbear.com/readyapi/docs/_images/testing/amqp-about.png)
- Exchange 유형
  - Direct, Fanout, Topic (패턴기반), Header
  - 각자 언제 사용?
    - Direct: 브로드캐스팅 (routing key 개념 알기)
    - Fanout: 심플 시나리오
    - Topic: 복잡한 시나리오
    - Header: 특별 필터링
- Why use RabbitMQ?
  - 클러스터링 
    - 노드가 fail 해도 안정
    - 공짜, 추가 플러그인 사용가능, 호환성 좋음, tracing 등 
- 비슷한 서비스 
  - ActiveMQ
  - AWS SQS
  - Azure Service Bus
- 설치 방법 
  - 윈도우: https://www.rabbitmq.com/install-windows.html
  - service.msc 에서 RabbitMQ 설정을 바꾼다. 

## QUIZ

- What are the different types of exchanges?
  - fanout, direct, topic, and headers
- To run RabbitMQ locally, you need to install `_____`.
  - Erlang
    - RabbitMQ needs the 64bit version of Erlang to run correctly.
- On which platform can you not install/run RabbitMQ?
  - Azure
  - RabbitMQ can run on all of these platforms. 정답
    - RabbitMQ is a message broker that runs on many platforms, not just these mentioned here.
  - Docker
  - Windows
- In case of `_____` , applications connecting to RabbitMQ will connect to `_____` nodes.
  - clustering; a single node
    - Applications will connect to a single node and reconnect to another node if the first node fails.
- Which feature does RabbitMQ provide?
  - a management UI
  - plugins
  - all of these answers 정답 
  - multiple messaging protocols
- What should you do when your system is so critical that you don't want to lose any messages?
  - Set up highly available queues.
    - Clustering 답 아닌 이유
      - Clustering ensures that applications can still continue to use RabbitMQ in case a node fails. But any messages still on the failing node will be lost. Highly Available Queues solve this problem. They will duplicate the messages across nodes and consumers will continue to receive messages when one node fails.
- True, False: If two applications subscribe to the same queue, they will both receive all messages.
  - False
    - RabbitMQ will load balance between consumers. This means only one consumer will receive the message. The next message will go the other consumer.
- A `_____` exchange is best suited when you need to notify all connected mobile devices.
  - fanout
    - A fanout exchange will ignore the routing key and send the message to all bound queues, and consequently to all connected applications.
    - direct 아닌 이유 
      - A direct exchange will filter messages based on an exact match of the routing key. While it is possible to use a direct exchange in this case, a fanout exchange would be a better match because the routing key is irrelevant here.
- In a headers exchange, the headers of the message must match all the arguments defined in the binding.
  - False
- For a direct exchange to route a message to a queue, the routing key must match the key of the binding exactly.
  - TRUE
- Which two exchanges ignore the routing key?
  - fanout and headers
- Why would you want a queue to be durable?
  - So that the queue isn't removed when the last client disconnects.
  - So that any messages on the queue survive a broker restart.
  - So that it is recreated after RabbitMQ restarts.
- Why would you want a queue to be durable?
  - so that the queue isn't removed when the last client disconnects.
    - 아닌 이유: This is the auto-delete property. Auto-delete means that a queue will be deleted when the last client disconnects. Durability means that a queue or exchange survives a broker restart.
  - So that any messages on the queue survive a broker restart. 
    - 아닌 이유:Unless you specifically configure RabbitMQ to do so, messages are lost when the broker restarts. Even with durable queues. Durability just means that you don't need to recreate the queues after a restart.
  - So that it is recreated after RabbitMQ restarts. 정답 
- A consumer should send an acknowledgement to the message broker after it receives a message.
  - TRUE
    - If the message broker doesn't receive an acknowledgement, the message will remain on the queue.
- Which statement is correct?
  - A publisher puts a message on the queue. Bindings will determine if a consumer receives the message.
  - A publisher puts a message on a queue. A binding then determines if the message is routed to an exchange. The consumer is subscribed to the exchange and receives the message.
  - A publisher sends a message to an exchange. The binding determines if a message is routed to a queue. And a consumer who is subscribed to a queue will receive the message from the queue. 정답 
    - This is what the AMQ model looks like.
- What is a binding?
  - It's the channel between the message broker and the consumer.
  - It's the key that is included in the message to determine which queue to route the message to.
    - 아닌 이유: The key that is included in the message is called the routing key. This can be used by the binding to determine if the message should be routed to the queue. The binding is the rule that makes this decision.
  - It's a rule that determines if a message is sent to a queue or not. 정답 
    - These rules are how exchanges and queues are connected. They can sometimes use the routing key or message headers.
- What is the difference between a binary and a text protocol?
  - In a text protocol the field names are also transmitted, so the order isn't important. In a binary protocol, data can be found at fixed positions. 정답 
  - Text protocols can only be used to transmit text. If you want to transmit binary data like images, you need a binary protocol.
    - 아닌 이유: Text protocols can also send binary data. The difference is that text protocols will also send the field names of the values they are transmitting.
  - None, it's all bytes as underlying technology anyway.
- True, False: In message-based systems, an application only needs to locate the message broker, not the other services.
  - True
    - The message broker should be configured correctly to send the messages to the other applications. The sending application only needs to send its message to the broker.
- What is one aspect of RPC-based systems?
  - Communication is asynchronous
  - Services must know the addresses of all their dependencies. 정답 
    - In RPC based systems, one service calls another directly. So it needs to know the address of every service that it wants to call.
  - Services are decoupled.









































