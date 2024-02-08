# Elasticsearch Mark

## 应用场景

- 全文检索
- 日志平台
- 监控平台
- 数据库查询加速
- GEO/GIS 地理搜索
- 向量智能搜索

## 核心概念

- `Cluster` 集群
- `Node` 节点
- `Index` 索引，逻辑存储空间
- `Shard` 分片，物理存储空间
- `Replicate` 副本
- `Segement` 分段
- `Document` 文档
- `Term` 词项，单个字段拆分成多个词

## 核心算法

- `Inverted Index` 倒排索引，用途：数据检索
- `Doc Value` 列式存储，与倒排相反，用途：数据排序、聚合统计
- `FST` 有限状态转换，Finite State Transducers，用途：前缀、后缀匹配
- `Skip List` 跳表，用途：文档定位跳跃
- `BKD_TREE` 多维空间树，用途：简单数值、范围数据
- `Roaring Bitmap` 压缩位图，用途：原始数据压缩、查询结果合并
- `TF/IDF/BM25` 用途：分值计算
  - `TF` 词频，= 某个词在文档中出现次数/文章中的总次数
  - `TF-IDF` 逆文档词频，= log(语料库的文档总数/(包含该词的文档数 + 1))
  - `BM25`，分值计算在一定范围内
- 集群选举算法
  - `Bully` 算法，版本：`6.8.*-`
  - `RAFT` 共识算法，版本：`7.0.*+`

## 安装与配置

### 硬件配置

### `Linux` 系统配置

### `jvm.options` 配置

### 安装

#### 单点

##### `elasticsearch.yml` 单点配置

```yaml

```

#### 集群

##### `elasticsearch.yml` 集群配置

```yaml

```
