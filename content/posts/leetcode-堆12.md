---
title: "leetcode-堆12"
date: 2026-08-21T09:54:11+08:00
tags: ["leetcode", "堆"]
draft: false
---


## 雇佣 K 名工人的最低成本

有 n 名工人。 给定两个数组 quality 和 wage ，其中，quality[i] 表示第 i 名工人的工作质量，其最低期望工资为 wage[i] 。

现在我们想雇佣 k 名工人组成一个 工资组。在雇佣 一组 k 名工人时，我们必须按照下述规则向他们支付工资：

1. 对工资组中的每名工人，应当按其工作质量与同组其他工人的工作质量的比例来支付工资。
2. 工资组中的每名工人至少应当得到他们的最低期望工资。

给定整数 k ，返回 组成满足上述条件的付费群体所需的最小金额 。与实际答案误差相差在 10^-5 以内的答案将被接受。


```
use std::collections::BinaryHeap;

impl Solution {
    /// 核心思路：按 wage/quality 比率排序，维护大小为 k 的最小 quality 和
    pub fn mincost_to_hire_workers(quality: Vec<i32>, wage: Vec<i32>, k: i32) -> f64 {
        let k = k as usize;
        let n = quality.len();

        // 按 wage/quality 比率升序排列，避免浮点精度问题，用交叉相乘比较
        let mut workers: Vec<(i32, i32)> = quality.into_iter().zip(wage).collect();
        workers.sort_unstable_by(|a, b| {
            (a.1 * b.0).cmp(&(b.1 * a.0))
        });

        // 最大堆维护当前选中的 k 个 quality，堆顶是其中最大的 quality
        let mut max_heap = BinaryHeap::new();
        let mut sum_q = 0i64; // 用 i64 防止溢出

        // 先选前 k 个作为初始组
        for &(q, _) in &workers[..k] {
            max_heap.push(q);
            sum_q += q as i64;
        }

        // 初始答案：用第 k-1 个工人的比率作为基准
        let mut ans = sum_q as f64 * workers[k - 1].1 as f64 / workers[k - 1].0 as f64;

        // 遍历后续工人，尝试用更小的 quality 替换当前组中最大的 quality
        for &(q, w) in &workers[k..] {
            if q < *max_heap.peek().unwrap() {
                sum_q -= *max_heap.peek().unwrap() as i64;
                max_heap.pop();
                max_heap.push(q);
                sum_q += q as i64;
                ans = ans.min(sum_q as f64 * w as f64 / q as f64);
            }
        }

        ans
    }
}
```
