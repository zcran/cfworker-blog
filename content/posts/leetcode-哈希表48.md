---
title: "leetcode-哈希表48"
date: 2026-10-09T20:07:58+08:00
tags: ["leetcode", "哈希表"]
draft: false
---


## 查找用户活跃分钟数

给你用户在 LeetCode 的操作日志，和一个整数 k 。日志用一个二维整数数组 logs 表示，其中每个 logs[i] = [IDi, timei] 表示 ID 为 IDi 的用户在 timei 分钟时执行了某个操作。

多个用户 可以同时执行操作，单个用户可以在同一分钟内执行 多个操作 。

指定用户的 用户活跃分钟数（user active minutes，UAM） 定义为用户对 LeetCode 执行操作的 唯一分钟数 。 即使一分钟内执行多个操作，也只能按一分钟计数。

请你统计用户活跃分钟数的分布情况，统计结果是一个长度为 k 且 下标从 1 开始计数 的数组 answer ，对于每个 j（1 <= j <= k），answer[j] 表示 用户活跃分钟数 等于 j 的用户数。

返回上面描述的答案数组 answer 。


```
use std::collections::{HashMap, HashSet};

impl Solution {
    pub fn finding_users_active_minutes(logs: Vec<Vec<i32>>, k: i32) -> Vec<i32> {
        // 按用户聚合其活跃的"唯一分钟"集合，去重后集合大小即该用户的 UAM。
        // 统计 UAM 恰好为 j 的用户数，放入下标 j-1。
        let mut user_minutes: HashMap<i32, HashSet<i32>> = HashMap::new();
        for log in &logs {
            user_minutes.entry(log[0]).or_default().insert(log[1]);
        }

        let mut ans = vec![0i32; k as usize];
        for minutes in user_minutes.values() {
            ans[minutes.len() - 1] += 1;
        }
        ans
    }
}
```
