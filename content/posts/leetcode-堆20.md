---
title: "leetcode-堆20"
date: 2026-08-21T09:54:11+08:00
tags: ["leetcode", "堆"]
draft: false
---


## 最大的团队表现值

给定两个整数 n 和 k，以及两个长度为 n 的整数数组 speed 和 efficiency。现有 n 名工程师，编号从 1 到 n。其中 speed[i] 和 efficiency[i] 分别代表第 i 位工程师的速度和效率。

从这 n 名工程师中最多选择 k 名不同的工程师，使其组成的团队具有最大的团队表现值。

团队表现值 的定义为：一个团队中「所有工程师速度的和」乘以他们「效率值中的最小值」。

请你返回该团队的​​​​​​最大团队表现值，由于答案可能很大，请你返回结果对 10^9 + 7 取余后的结果。


```
use std::cmp::Reverse;
use std::collections::BinaryHeap;

impl Solution {
    /// 贪心：按效率降序遍历，维护最多 k 个最大速度的堆
    pub fn max_performance(n: i32, speed: Vec<i32>, efficiency: Vec<i32>, k: i32) -> i32 {
        const MOD: i64 = 1_000_000_007;

        // 按效率降序排列：(efficiency, speed)
        let mut engineers: Vec<(i32, i32)> = efficiency.into_iter().zip(speed).collect();
        engineers.sort_unstable_by_key(|&(e, _)| Reverse(e));

        let k = k as usize;
        let mut heap = BinaryHeap::new(); // 小根堆，存速度，堆顶最小
        let mut sum_speed: i64 = 0;
        let mut ans: i64 = 0;

        for (e, s) in engineers {
            heap.push(Reverse(s));
            sum_speed += s as i64;

            // 超过 k 人，移除速度最小的
            if heap.len() > k {
                if let Some(Reverse(min_s)) = heap.pop() {
                    sum_speed -= min_s as i64;
                }
            }

            // 当前效率为团队最低效率，计算表现值
            ans = ans.max(sum_speed * e as i64);
        }

        (ans % MOD) as i32
    }
}
```
