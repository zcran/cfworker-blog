---
title: "Leetcode 随机化14"
date: 2024-12-21T15:03:43+08:00
tags: ["leetcode", "随机化"]
draft: false
---

## 按权重随机选择

给定一个正整数数组 w ，其中 w[i] 代表下标 i 的权重（下标从 0 开始），请写一个函数 pickIndex ，它可以随机地获取下标 i，选取下标 i 的概率与 w[i] 成正比。

例如，对于 w = [1, 3]，挑选下标 0 的概率为 1 / (1 + 3) = 0.25 （即，25%），而选取下标 1 的概率为 3 / (1 + 3) = 0.75（即，75%）。

也就是说，选取下标 i 的概率为 w[i] / sum(w) 。

```
use rand::{thread_rng, Rng};

struct Solution {
    sum: Vec<i32>,  // 权重的前缀和数组
    total: i32,  // 所有权重的总和
}

impl Solution {

    fn new(w: Vec<i32>) -> Self {
        let mut sum = vec![0;w.len()];
        let mut total = w[0];
        sum[0] = w[0];

        for i in 1..w.len() {
            sum[i] = sum[i-1] + w[i];
            total += w[i];
        }

        Self {
            sum, 
            total,  
        }
    }
    
    fn pick_index(&self) -> i32 {
        let mut rng = thread_rng();
        let target: i32 = rng.gen_range(0..self.total) + 1;

        self.binary_search(target)
    }

    fn binary_search(&self, target: i32) -> i32 {
        let n = self.sum.len();
        let mut lo = 0;
        let mut hi = n - 1;

        while lo < hi && hi < n {
            let mid = lo + (hi - lo) / 2;
            if self.sum[mid] < target {
                lo = mid + 1;
            } else {
                hi = mid;
            }
        }

        lo as i32
    }
}
```
#### 工作原理
假设权重数组是 [1, 3, 2]，则：
前缀和数组为 [1, 4, 6]
总权重为 6
随机生成 1-6 的数字：
1 → 选择索引 0
2-4 → 选择索引 1
5-6 → 选择索引 2
这样索引 0 有 1/6 概率，索引 1 有 3/6 概率，索引 2 有 2/6 概率
#### 使用场景
这种数据结构常用于：
加权随机抽样
游戏中的随机事件触发（不同事件不同概率）
负载均衡（不同服务器按权重分配请求）
#### 复杂度分析
构造时间：O(n)
空间：O(n)
查询时间：O(log n)
相比简单的线性搜索方法（O(n)），这种实现使用二分查找提高了查询效率。

函数式改进
```
use rand::{thread_rng, Rng};

struct Solution {
    sum: Vec<i32>,
    total: i32,
}

impl Solution {
    fn new(w: Vec<i32>) -> Self {
        let sum = w.iter()
            .scan(0, |state, &x| {
                *state += x;
                Some(*state)
            })
            .collect::<Vec<_>>();
        
        let total = sum.last().copied().unwrap_or(0);

        Self { sum, total }
    }
    
    fn pick_index(&self) -> i32 {
        let target = thread_rng().gen_range(1..=self.total);
        self.sum
            .binary_search(&target)
            .unwrap_or_else(|i| i) as i32
    }
}
```