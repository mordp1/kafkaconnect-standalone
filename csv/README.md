# kafkaconnect-standalone CSV
### Example to use Kafka Connect in standalone mode to load CSV file from source to Kafka Cluster in AIVEN + AVRO + Schema Registry.
==============

- We can use the image from [Confluent - image: confluentinc/cp-kafka-connect-base:6.1.0](https://docs.confluent.io/platform/current/quickstart/ce-docker-quickstart.html), but I exclude others services and use just Kafkacat and Kafka Connect: [docker-compose.yml](https://github.com/mordp1/kafkaconnect-standalone/blob/main/csv/docker-compose.yml). I recommend you to take a look this line and adapt [docker-compose.yml#L27](https://github.com/mordp1/kafkaconnect-standalone/blob/53104607a1a6da976e2686fb023e4f4ffce97977/csv/docker-compose.yml#L27) and [docker-compose.yml#L39](https://github.com/mordp1/kafkaconnect-standalone/blob/53104607a1a6da976e2686fb023e4f4ffce97977/csv/docker-compose.yml#L39)

- We will use 2 differents ways to load CSV file and push to Kafka: First config [csv.properties](https://github.com/mordp1/kafkaconnect-standalone/blob/main/csv/csv.properties) you can check all instructions here [Loading CSV data into Kafka - Robin Moffatt](https://rmoff.net/2020/06/17/loading-csv-data-into-kafka/) and [CSV Source Connector for Confluent Platform](https://docs.confluent.io/kafka-connect-spooldir/current/connectors/csv_source_connector.html), second config [csv2.properties](https://github.com/mordp1/kafkaconnect-standalone/blob/main/csv/csv2.properties) you can check all instructions here [Connect FS](https://kafka-connect-fs.readthedocs.io/en/latest/).

- For this example, I've created free cluster on https://aiven.io/ and we need to configure some things to connect on these servers.

- You can check more info how can connect standalone Kafka connect to AIVEN [Bring your own Apache Kafka® Connect cluster](https://developer.aiven.io/docs/products/kafka/kafka-connect/howto/bring-your-own-kafka-connect-cluster.html)


### To execute:
```
  git clone https://github.com/mordp1/kafkaconnect-standalone
  cd kafkaconnect-standalone
  docker-compose up -d
 ```

 ### Preparations:
 - In your https://console.aiven.io/ should download *Access Key*, *Access Certificate* and *CA Certificate* and we will create Keystores and truststores. 
 - This link will help [Configure Java SSL to access Apache Kafka - AIVEN](https://developer.aiven.io/docs/products/kafka/howto/keystore-truststore.html). 
 - Activated Schema Registry in your AIVEN Kafka cluster and I recommend using the same password regarding *avnadmin* for Keystores and truststores. 
 - Save all files in kafkaconnect-standalone/csv.


 ### Adapt the properties with your configs
 - You can use this example [connect.properties](https://github.com/mordp1/kafkaconnect-standalone/blob/main/csv/connect.properties) and change according to your settings.
 - Create 2 topics inside AIVEN Kafka cluster: *cartopic* and *ordertopic*
 - Download [kafka-connect-spooldir](https://www.confluent.io/hub/jcustenborder/kafka-connect-spooldir) and [kafka-connect-fs](https://www.confluent.io/hub/mmolimar/kafka-connect-fs). Save and decompress in kafkaconnect-standalone/csv the files.
 
 ### Connect to container
```
  docker ps
  docker exec -ti kafka-connect /bin/bash
  $ cd /data/
 ```

 ### Execute Kafka connect standalone mode using csv.properties
 Check this link for details regarding the config connector [Loading CSV data into Kafka - Robin Moffatt](https://rmoff.net/2020/06/17/loading-csv-data-into-kafka/).
 Basically, connector loads all CSV files inside /data/unprocessed and after processed will move to /data/processed.
 ```
  $ connect-standalone connect.properties csv.properties
 ```

### Using Kafkat to consume messages. Change according to your settings.
 ```
   docker exec -ti kafkacat /bin/sh
   $ cd /data/
   $ /usr/bin/kafkacat -F kcat.config -C -t ordertopic -l -s avro -r https://avnadmin:AVNS_nlbe5HYiT6E42ev1VBn@kafka-mordp1-marcos-9abb.aivencloud.com:22671 -J | jq
 ```

### Execute Kafka connect standalone mode using csv2.properties
Check this link for details regarding the config connector [kafka-connect-fs](https://kafka-connect-fs.readthedocs.io/en/latest/).
Basically, connector loads car.csv file inside /data.
 ```
  $ connect-standalone connect.properties csv2.properties
 ```

### Using Kafkat to consume messages. Change according to your settings.
 ```
   docker exec -ti kafkacat /bin/sh
   $ cd /data/
   $ /usr/bin/kafkacat -F kcat.config -C -t cartopic -l -s avro -r https://avnadmin:AVNS_nlbe5HYiT6E42ev1VBn@kafka-mordp1-marcos-9abb.aivencloud.com:22671 -J | jq
 ```

### Extra
- AIVEN github have an example regarding a producer/consumer to do the same. Can check here [kafka-schema-registry](https://github.com/aiven/aiven-examples/tree/master/solutions/kafka-schema-registry)
- https://github.com/aiven/aiven-examples
