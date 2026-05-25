---
title: "leetcode-队列18"
date: 2026-05-23T09:21:38+08:00
tags: ["leetcode", "队列"]
draft: false
---


## 设计路由器

请你设计一个数据结构来高效管理网络路由器中的数据包。每个数据包包含以下属性：

· source：生成该数据包的机器的唯一标识符。
· destination：目标机器的唯一标识符。
· timestamp：该数据包到达路由器的时间戳。

实现 Router 类：

Router(int memoryLimit)：初始化路由器对象，并设置固定的内存限制。

· memoryLimit 是路由器在任意时间点可以存储的 最大 数据包数量。
· 如果添加一个新数据包会超过这个限制，则必须移除 最旧的 数据包以腾出空间。

bool addPacket(int source, int destination, int timestamp)：将具有给定属性的数据包添加到路由器。

· 如果路由器中已经存在一个具有相同 source、destination 和 timestamp 的数据包，则视为重复数据包。
· 如果数据包成功添加（即不是重复数据包），返回 true；否则返回 false。


int[] forwardPacket()：以 FIFO（先进先出）顺序转发下一个数据包。

· 从存储中移除该数据包。
· 以数组 [source, destination, timestamp] 的形式返回该数据包。
· 如果没有数据包可以转发，则返回空数组。

int getCount(int destination, int startTime, int endTime)：

· 返回当前存储在路由器中（即尚未转发）的，且目标地址为指定 destination 且时间戳在范围 [startTime, endTime]（包括两端）内的数据包数量。

注意：对于 addPacket 的查询会按照 timestamp 的非递减顺序进行。


```
use std::collections::{HashMap, HashSet, VecDeque};

/// 数据包：源地址、目标地址、时间戳
#[derive(Hash, Eq, PartialEq, Clone)]
struct Packet(i32, i32, i32);

/// 网络路由器
struct Router {
    cap: usize,                         // 内存容量（最大包数）
    queue: VecDeque<Packet>,            // FIFO 队列，存储所有包（按到达顺序）
    set: HashSet<Packet>,               // 快速去重 O(1)
    dest_map: HashMap<i32, VecDeque<i32>>, // 每个目标地址的时间戳队列（有序，用于范围计数）
}

impl Router {
    /// 创建路由器
    pub fn new(memory_limit: i32) -> Self {
        Router {
            cap: memory_limit as usize,
            queue: VecDeque::new(),
            set: HashSet::new(),
            dest_map: HashMap::new(),
        }
    }

    /// 添加数据包（非重复时添加，若内存已满则先丢弃最旧包）
    /// 返回 true 表示成功，false 表示重复
    pub fn add_packet(&mut self, source: i32, destination: i32, timestamp: i32) -> bool {
        let pkt = Packet(source, destination, timestamp);
        if self.set.contains(&pkt) {
            return false;
        }
        // 容量满时，移除最旧包（FIFO）
        if self.queue.len() == self.cap {
            self.forward_packet(); // 忽略返回值，仅移除
        }
        // 插入新包
        self.queue.push_back(pkt.clone());
        self.dest_map
            .entry(destination)
            .or_insert_with(VecDeque::new)
            .push_back(timestamp);
        self.set.insert(pkt);
        true
    }

    /// 转发最旧的数据包（FIFO），返回 [source, destination, timestamp]
    /// 若无包可转发，返回空 Vec
    pub fn forward_packet(&mut self) -> Vec<i32> {
        let pkt = match self.queue.pop_front() {
            Some(p) => p,
            None => return Vec::new(),
        };
        self.set.remove(&pkt);
        if let Some(q) = self.dest_map.get_mut(&pkt.1) {
            q.pop_front(); // 同步移除目标地址的对应时间戳
        }
        vec![pkt.0, pkt.1, pkt.2]
    }

    /// 统计目标地址在 [start_time, end_time] 内的未转发包数量
    pub fn get_count(&self, destination: i32, start_time: i32, end_time: i32) -> i32 {
        let Some(q) = self.dest_map.get(&destination) else { return 0 };
        // 由于添加时 timestamp 非递减，队列有序，可用二分查找
        let left = q.partition_point(|&t| t < start_time);
        let right = q.partition_point(|&t| t <= end_time);
        (right - left) as i32
    }
}
```
