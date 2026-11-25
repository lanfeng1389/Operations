# 调整clickhouse kafka引擎

**这个调整是为了优化clickhouse不让重启**

**引擎kafka\_num\_consumers配置，单个表的消费者数量调整为1**

```javascript
#进入chi-clickhouse-clusterpod
kubectl get pod -n prometheus
kubectl exec -it chi-clickhouse-cluster-default-0-0-0 -n prometheus sh
#链接clickhouse
clickhouse-client
#调整events kafka引擎表配置（删除，重建表）
use gta
show tables

#删除kafka表引擎
DROP TABLE kafka_gta_events_str
#重建引擎
CREATE TABLE IF NOT EXISTS gta.kafka_gta_events_str on cluster default(`line`String)ENGINE = Kafka('kafka.middleware.svc.cluster.local:9092','gta_events','gta-clickhouse-consumer','JSONAsString')SETTINGS kafka_num_consumers = 1,kafka_skip_broken_messages = 1000;

#调整perf kafka引擎表配置
DROP TABLE kafka_gta_events_perf_str
CREATE TABLE IF NOT EXISTS gta.kafka_gta_events_perf_str on cluster default(`line`String)ENGINE = Kafka('kafka.middleware.svc.cluster.local:9092','gta_events_performance','gta-performance-clickhouse-consumer','JSONAsString')SETTINGS kafka_num_consumers = 1,kafka_skip_broken_messages = 1000;

#调整crash kafka引擎表配置
DROP TABLE kafka_gta_events_crash_str
CREATE TABLE IF NOT EXISTS gta.kafka_gta_events_crash_str on cluster default(`line`String)ENGINE = Kafka('kafka.middleware.svc.cluster.local:9092','gta_events_crash','gta-crash-clickhouse-consumer','JSONAsString')SETTINGS kafka_num_consumers = 1,kafka_skip_broken_messages = 1000;
```