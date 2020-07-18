---
title: 初识Redis
date: 2020-04-20 19:53:45
tags: 学习笔记·Redis  
---
## 写在前面   
2020年四月末，在教程视频中，第一次简单了解了Redis，在此记录其间的部分知识点  

<!-- more -->  
1. 说说你用过的Redis的数据类型  
供用户使用的数据类型  
    - String：最基本的数据类型，二进制安全  
    - Hash：String元素组成的字典，适合用于存储对象  
    - List：列表，按照String元素插入顺序排序  
    - Set：String元素组成的无序集合，通过哈希表实现，不允许重复  
    - Sorted Set：通过分数来为集合中的成员进行从小到大的排序
    - 用于计数的HyperLogLog，用于支持存储地理位置信息的Geo 

    （记前五个）  

2. 如何解决SETNX长期有效的问题  
EXPIRE key seconds  
设置key的生存时间，当key过期时（即生存时间为0），会被自动删除  

3. 如何通过Redis实现分布式锁  
SET key value [EX seconds] [PX milliseconds] [NX|XX]  
    - EX second：设置键的过期时间为second秒  
    - PX millisecond：设置键的过期时间为millisecond毫秒  
    - NX：只在键不存在时，才对键进行设置操作  
    - XX：只在键已经存在时，才对键进行设置操作  
    - SET操作完成时，返回OK，否则返回nil  

4. 大量的key同时过期的注意事项  
集中过期，由于清除大量的key很耗时，会出现短暂的卡顿现象  
解决方案：在设置key的过期时间的时候，给每个key加上随机值  

5. pub/sub的缺点  
消息的发布是无状态的，无法保证可达。  

6. 自动化触发RDB持久化的方式  
    - 根据redis.conf配置里的SAVE m n 定时触发（用的是BGSAVE）  
    - 主从复制时，主节点自动触发  
    - 执行Debug Reload  
    - 执行Shutdown且没有开启AOF持久化  

7. 