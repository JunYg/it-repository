# Kafka Streams

- 轻量

## 概念

**Kafka Streams:**

- Kafka 客户端
- 属于 Kafka 生态
- 轻量级实时处理框架

**Kafka Streams 特征：**

**Kafka Streams 术语：**

- DAF
- DAG，有向无环图
- Source Processor
- Stream Processor
- Sink Processor
- Topology
  - Sub Topologies
- Streams Task
- Stream Thread

**Kafka 并发模型：**

深度优先处理策略：一条记录会路由给每个处理器后再处理下一条，一条记录处理完后才会处理下一条记录。

数据处理策略：一个拓扑处理中，不会同事处理多条记录。

## 无状态、有状态

### Stateless Streams

> 无状态，消息之间没有关系，相互独立

#### Stateless Operation

- Map
- MapValues
- Filter
- FilterNot
- FlatMap
- FlatMapValues
- **SelectKey**，内容不变设置新 key
- Split-Branch/DefaultBranch
- Merge
- To
- Peek
- ForEach，terminal
- Print，terminal

### Statefull Streams

> 有状态的

**State store：**

- Kafka Streams 在任务层面嵌入到应用中。
- state stores 使用 RocksDB
- state stores 会在 Kafka 的 changelog topics 做备份

#### Stateful Operation

- Repartition，重新分区，会降低性能
  - 重新分区后 topic 的分区数与原来的分区数相同
  - 重新分区用的 topic 会自动创建清理
  - 尽量避免重分区，最好在原始 topic 按 key 做好分区
- Transform，不会主动触发 repartition
- TransformValues
- FlatTransform
- FlatTransformValues
- **Joining**，支持 streams 和 tables

## KTable & GlobalTable
