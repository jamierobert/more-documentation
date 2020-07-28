### Toxi-Proxy

Server Listening on 172.18.22.0:8474

	ToxiproxyClient client = new ToxiproxyClient("172.18.22.0", 8474);

toxiproxy-cli create kafka_test_kafka_1 -l toxiproxy:24001 -u kafka1:24001

	Proxy kafkaProxy = client.createProxy("kafka_test_kafka_1", "toxiproxy:24001", "kafka1:24001");
	
	Proxy kafkaProxy = client.createProxy("kafka_test_kafka_2", "toxiproxy:24002", "kafka1:24002");
	
	Proxy kafkaProxy = client.createProxy("kafka_test_kafka_1", "toxiproxy:24003", "kafka1:24003");


When bringing up the project - bring up all containers and tail the logs until all apps are up - once up we can post requests through and tail the camel consumer logs


When we kill node3 or node2 

Connection to node 3 could not be established. Broker may not be available.

When we kill kafka1 we get complete failiure - and no messages are received by the consumer, but the recovery is good - all messages are received when the leader comes back up.

I've tried:

- Stopping container with docker-compose.
- Stopping with docker and removing container.
- Stopping with Toxi-Proxy

All scenarios consumed all messages once reconnected. Even when we were looking at a fresh container.
Seems to work well at 2 and 5 seconds of latency added using toxiproxy.

[ToxiProxy]
(https://medium.com/@ravindraprasad/testing-known-unknowns-using-toxiproxy-75dfc9d0dc1)
	
	toxiproxy-cli ls
	
	toxiproxy-cli toxic add test_kafka_1 -t latency -a latency=5000
	
	toxiproxy-cli toxic remove test_kafka_1 --toxicName latency_dowstream
	
	toxiproxy-cli toggle test_kafka_1



