---
title: 分布式KV
date: 2025-06-26 15:54:01
tags: 
  - Golang
  - 分布式
  - KV存储
top: 9
---

# 分布式KV

## 简历描述

分布式 Key-Value 存储系统 - Golang

- 基于 Raft 协议实现多节点自动选主与故障恢复，Leader 选举平均耗时 < 200ms，故障恢复时间 < 1s（3 节点集群）
- 通过 ReadIndex 机制优化线性化读，降低 P99 延迟从 50ms → 15ms。
- 持久化层集成 PebbleDB，支持高吞吐写入与压缩优化。引入 Redis 热点缓存，降低高频访问数据的尾延迟。
- 定期快照压缩（默认每10000条），减少日志回放开销与存储占用。
- 原生支持 Kubernetes，通过 StatefulSet + Headless Service 实现节点动态注册与服务发现。
- 支持分区路由 与 Region 分裂 （阈值64MB），实现水平扩展与负载均衡。

## 参考

[TiKV Storage](https://tikv.org/docs/6.1/reference/architecture/storage/)

[TiKV 架构](https://docs.pingcap.com/zh/tidb/stable/rocksdb-overview/)

[Partitioned Raft KV 原理解析](https://cloud.tencent.com/developer/article/2287341)

![TiKV RocksDB](https://docs-download.pingcap.com/media/images/docs-cn/tikv-rocksdb.png)

![Raft in TiKV diagram](https://tikv.org/img/docs/tikv-storage-1.png)

![TiKV Storage diagram](https://tikv.org/img/docs/tikv-storage-3.png)

## 问题

> 基于 Raft 协议实现多节点自动选主与故障恢复，Leader 选举平均耗时 < 200ms，故障恢复时间 < 1s（3 节点集群）

1. **Raft 的核心流程是怎样的？和 Paxos 有什么区别？**
2. **Leader 是如何选出来的？你是如何测得“平均耗时 < 200ms”？，是多台物理机部署的结果吗**
3. **什么情况下Leader会崩溃？**
4. **Raft 中 Leader 崩溃后怎么恢复？你是怎么实现 <1s 的恢复时间？**
5. **3 节点下 Raft 的容错上限是多少？什么情况下系统不可用？**
6. **如果网络分区发生，Raft 如何保证一致性？你有模拟过网络分区测试吗？**
7. **Raft 中如何处理脑裂问题？**

> 通过 ReadIndex 机制优化线性化读，降低 P99 延迟从 50ms → 15ms。

1. **有考虑时钟漂移的问题吗？**
2. **ReadIndex 是什么？它是怎么保证线性一致性的？**
3. **与传统的“通过 Leader 写空日志”读方式比，ReadIndex 有什么优势？**
4. **ReadIndex 和 Lease Read 区别？为何选择 ReadIndex？**
5. **你是如何测量并优化 P99 延迟的？压测工具和测试流程是？**
6. **线性读和弱一致读有什么区别？是否支持弱一致性读取？**

> 持久化层集成 PebbleDB，支持高吞吐写入与压缩优化。引入 Redis 热点缓存，降低高频访问数据的尾延迟。

1. **为什么选择 Pebble 而不是 RocksDB？它们的差异是什么？**
2. **Pebble 是基于 LSM-Tree 的，它在写入性能上的优势体现在哪？**
3. **WAL的落盘怎么做，遇到断电问题怎么解决，这个日志具体是写在哪里的？**
4. **压缩策略你是如何配置的？是否手动调优过？**
5. **Redis 缓存是如何集成的？缓存一致性怎么保证？**
6. **测试的时候，数据量大概有多少**
7. **如何识别热点数据？是否使用了 TTL、LRU、LFU 等策略？**
8. **你有测过 Redis 缓存命中率吗？提升了多少尾延迟？**

> 定期快照压缩（默认每10000条），减少日志回放开销与存储占用。

1. **Raft 快照的触发机制是？你怎么选“10000 条”这个阈值的？**
2. **快照如何生成与持久化？是同步写还是异步？**
3. **如果在快照过程中宕机怎么办？是否支持快照恢复中断续传？**
4. **快照文件与日志的管理机制？如何避免双写冲突？**
5. **日志回放慢的问题具体是怎么暴露的？优化效果如何？**

> 原生支持 Kubernetes，通过 StatefulSet + Headless Service 实现节点动态注册与服务发现。

1. **动态注册和服务发现怎么做的？**
2. **为什么使用 StatefulSet 而不是 Deployment？**
3. **Headless Service 在这里的作用是什么？与普通 Service 有什么区别？**
4. **Raft 节点之间如何自动发现对方？Pod 重启或 IP 变化如何处理？**
5. **在 K8s 中如何实现挂掉的 Leader 自动重新加入集群？**
6. **是否使用 PVC 实现状态持久化？怎么保证数据不丢失？**
7. **你有做过滚动升级吗？升级期间如何保障服务可用性？**

> 支持分区路由 与 Region 分裂（阈值64MB），实现水平扩展与负载均衡。

1. **你是如何做分区设计的？基于 Key Hash 还是 Range？**
2. **Region 分裂的逻辑是什么？阈值 64MB 是如何定的？**
3. **分裂后如何更新路由信息？客户端如何感知变更？**
4. **Region 重叠或热点 Region 怎么处理？有没有做 Rebalance？**
5. **分裂操作是否阻塞写入？如何避免写时抖动？**
6. **支持 Region 合并吗？什么时候会触发？**

> 通用问题

1. **你这个项目更偏向 TiKV 还是 Etcd 的设计？主要参考了哪些系统？**
2. **整体 QPS 或写入吞吐你测试过吗？是如何压测的？**
3. **故障模拟做过哪些？节点宕机、网络延迟、磁盘满？**
4. **如果现在扩展到 100+ 节点，有哪些设计需要调整？**
5. **这个系统目前支持哪些客户端操作？Put/Get/Delete？事务？TTL？**
6. **和 Etcd、Redis Cluster、TiKV 等系统相比你的设计优劣点在哪里？**
