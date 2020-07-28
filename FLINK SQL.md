# FLINK SQL

Need to run the SQL Client....

If flink installed locally.

`../../flink-1.8.0/bin/sql-client.sh embedded --jar target/flink-training-exercises-2.8.0.jar -e sql-client-config.yaml`

If using the training environment:

`docker-compose exec sql-client ./sql-client.sh`

To Monitor Kafka in training environment:

`docker-compose exec sql-client /opt/kafka-client/bin/kafka-console-consumer.sh --bootstrap-server kafka:9092 --topic TenMinPsgCnts --from-beginning`



## Intro Training

#### Source Tables

Can be used in `FROM` clause can not be used in `INSERT`.

All source tables are external tables stored in separate Apache Kafka topics. The Kafka records are encoded in JSON. Each record that is read from a topic is appended to the corresponding source table.

#### Sink Tables

Can be used in `FROM` clause can not be used in `INSERT`.