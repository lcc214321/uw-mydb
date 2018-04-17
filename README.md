[TOC]
# 项目说明
全新设计的mysql分库分表中间件。产品计划使用netty 4.1开发，基于spring boot/cloud提供管理接口。  
支持nio和zero-copy的数据透传；支持数据分库、分表以及复合的分库分表功能；分片算法上除支持常见的hash/murmurHash，mod，range，date，code算法外，还额外支持强行指定。

# 竞品调查
1. mycat。功能复杂，分表支持不好，转发效率80%。1.x版本现在仍然一堆bug，无人维护，2.x版本开发中，还不支持分库分表。 
原计划参与mycat2.x的开发，但是被恶心了一下，想参与开发必须参加leader-us组织的收费培训。。。
2. kingshard。使用go编写，特色是支持单库分表，但是整体功能简单，路由特性较少，性能尚可。
3. sharding-jdbc。当当开发，集群试用下存在连接数问题，很要命，和我们现在的问题一样。

# 项目特色
1. 基于netty 4.1开发，全面支持zero-copy，流式转发。
2. 基于spring-cloud支持集群配置，并在此基础上提供web管理。
3. 支持多级路由链，可以在hash/range分库分表后，再次使用date分表。
4. 更强大的sql统计功能，可以统计前后端sql的执行信息。
5. 支持对mysql主机性能的检测，比如tps和总体延时。
6. 更高性能的连接池机制，更少的锁。
7. 基于mysql主机的连接控制，而非数据库层面的，更好地维护连接扩展性。

# 暂时放弃的功能
所有可能造成不稳定、降低性能、在中间件实现别扭的功能，可能都不会实现了。
1. 不支持在中间件层面支持数据聚合、排序等功能，因为没有必要，且造成不稳定。
2. 放弃事务管理，因为无法准确实现分布式事务，且可能造成不稳定。
3. 不支持last_insert_id()，因为对于集群应用毫无价值。

# 路由功能说明
1.所有不在路由名单里的SQL，直接发到schema的base node上，以满足某些客户端的需要。
1.对于所有的dml，在路由名单里的，不指定参数，默认不予执行，可以配置转发参数。
2.对于所有的ddl，在路由名单里的，不指定参数，默认不予执行，可以配置转发参数。

# DML SQL支持
SELECT 支持
UPDATE 支持
DELETE 支持
REPLACE 暂不支持
INSERT 支持单value，多value优化中。

# DDL SQL 支持
ALTER TABLE 支持
CREATE TABLE/INDEX 支持
DROP TABLE/INDEX 支持
TRUNCATE TABLE  支持

# 在虚拟schema上支持
SET 
SHOW 
EXPLAIN

## 基于分库的算法
### hash 哈希分库算法

### range 基于范围分库

### preset 预设值分库算法
preset可以覆盖hash/range的值

## 基于分表的算法
### date 基于日期的分表算法
可以合用
### code 基于代码的分表算法
独立使用
### range 基于范围的分表算法
独立使用