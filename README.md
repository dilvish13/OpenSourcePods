# OpenSourcePods
## FOR <b>Yandex</b> Managed Service for Kubernetes
https://console.cloud.yandex.ru/

Cluster: sample-service-cluster
Node group: sample-service-nodes-1
Node count: 1

Configurations for k8s deployments:
## reactive-sandbox
Kafka + ES + Cassandra services.

*Image:* `lightbend-docker-registry.bintray.io/rp/reactive-sandbox`:0.2.0

#### Settings
Change literals:
```
<deployment> "namespace": "<test>"
"name": "reactive-sandbox-test-reactive-sandbox"
"app": "reactive-sandbox"
"release": "reactive-sandbox-test"
<deployment> "hostPort": (optionally) specify to use without ingress
```
Switch on/off services in deployment
``` 
              {
                "name": "RS_ENABLE_CASSANDRA",
                "value": "1"
              },
              {
                "name": "RS_ENABLE_ELASTICSEARCH",
                "value": "1"
              },
              {
                "name": "RS_ENABLE_KAFKA",
                "value": "1"
              },
              {
                "name": "RS_ENABLE_ZOOKEEPER",
                "value": "1"
              },
```
##### Ingress for ES service
Change literals
```
"namespace": "<test>"
"serviceName": "reactive-sandbox-test-reactive-sandbox-elasticsearch"
<indexName>
<docTypeName> (similar to indexName)

```
##### ES endpoints
* `/<indexName>` - [PUT] index;
* `/<indexName>/<docTypeName>` - [PUT] data;
* `/<indexName>/<docTypeName>/_search` - [POST] search data query;
* `/<indexName>/_search` - [POST] search data query;
* `/_cat/health` - [GET] liveness probe;
* `/_all` - [GET] indices data;

## kafka
### KafkaHQ
Provides simple operation as view/delete topics, view subscribers and messages.

*Image:* `tchiotludo/kafkahq`

#### Settings
Change literals:
```
"namespace": "<test>"
"name": "kafkahq"
"app": "kafkahq"
<service> "nodePort": <port-hq> - specify if need direct access to port
<service> "type": "NodePort" - specify if need direct access to port
<service> "type": "ClusterIP"- specify if do not need direct access to port
```
Check enviroment variable in deployment:
```           
              {
                "name": "KAFKAHQ_CONFIGURATION",
                "value": "\"kafkahq\": {\"connections\": {\"kafkahq\": {\"properties\": {\"bootstrap.servers\": \"reactive-sandbox-test-reactive-sandbox-kafka:9092\"}}}}\n"
              }
```


### Kafka manager
Provides a bit less functionality comparing with _KafkaHQ_. May be required to config cluster connection in web interface.

*Image:* `zenko/kafka-manager`:1.3.3.22

#### Settings
Change literals:
```
"namespace": "<test>"
"name": "kafka-manager-test-kafka-manager"
"app": "kafka-manager"
"release": "kafka-manager-test"
<deployment> "serviceAccountName": "kafka-manager-test-kafka-manager",
<deployment> "serviceAccount": "kafka-manager-test-kafka-manager",
<service> "nodePort": <port-manager> - specify if need direct access to port
<service> "type": "NodePort" - specify if need direct access to port
<service> "type": "ClusterIP"- specify if do not need direct access to port
```
Check enviroment variable in deployment:
```
              {
                "name": "ZK_HOSTS",
                "value": "reactive-sandbox-test-reactive-sandbox-zookeeper:2181"
              }
```

## cassandra
Cassandra Web. Supports CQL requests.

*Image:* `delermando/docker-cassandra-web`:v0.4.0

#### Settings
Change literals
```
"namespace": "<test>"
<deployment> "kubectl.kubernetes.io/last-applied-configuration" >> {\"name\":\"CASSANDRA_HOST_IP\",\"value\":\"reactive-sandbox-test-reactive-sandbox-zookeeper.test\"}
<service> "nodePort": <port-web> - specify if need direct access to port
<service> "type": "NodePort" - specify if need direct access to port
<service> "type": "ClusterIP"- specify if do not need direct access to port
```
Change ip after every cassandra service pod recreation:
```
              {
                "name": "CASSANDRA_HOST_IP",
                "value": "<ip>"
              },
```


## elastic
Elastic HQ.

*Image:* `elastichq/elasticsearch-hq`

#### Settings
Change literals:
```
"namespace": "<test>"
<service> "nodePort": <port-hq> - specify if need direct access to port
<service> "type": "NodePort" - specify if need direct access to port
<service> "type": "ClusterIP"- specify if do not need direct access to port
```
Change ip after every ES pod recreation:
```
             {
                "name": "test_host",
                "value": "<ip>"
              },
```