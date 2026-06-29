---
title: "leetcode-有序集合37"
date: 2026-06-14T10:47:42+08:00
tags: ["leetcode", "有序集合"]
draft: false
---


## 使二进制字符串全为 1 的最少操作次数

给你一个二进制字符串 s 和一个整数 k。

在一次操作中，你必须选择 恰好 k 个 不同的 下标，并将每个 '0' 翻转 为 '1'，每个 '1' 翻转为 '0'。

返回使字符串中所有字符都等于 '1' 所需的 最少 操作次数。如果不可能，则返回 -1。


```
use std::collections::VecDeque;
use std::ops::Bound;

impl Solution {
    pub fn min_operations(s: String, k: i32) -> i32 {
        let k = k as usize;
        let n = s.len();
        let s = s.as_bytes();

        // 统计初始 '0' 的数量
        let start = s.iter().filter(|&&c| c == b'0').count();

        // 如果已经全为 '1'
        if start == 0 {
            return 0;
        }

        // BFS 状态: 当前字符串中 '0' 的数量
        // 使用 BTreeSet 存储未访问的状态，按奇偶性分组
        use std::collections::BTreeSet;
        let mut unvisited = [BTreeSet::new(), BTreeSet::new()];

        // 初始化所有可能的状态 [0, n]
        for m in 0..=n {
            unvisited[m & 1].insert(m);
        }

        // 起点已访问
        unvisited[start & 1].remove(&start);

        let mut queue = VecDeque::new();
        queue.push_back(start);

        let mut steps = 0;

        while !queue.is_empty() {
            let mut next_queue = VecDeque::new();

            while let Some(z) = queue.pop_front() {
                if z == 0 {
                    return steps;
                }

                // 计算可达状态范围
                // 选择翻转 x 个 '0' 和 k-x 个 '1'
                // x ∈ [max(0, k-(n-z)), min(k, z)]
                // 新 '0' 数量 = z + k - 2x
                let min_x = if k > n - z { k - (n - z) } else { 0 };
                let max_x = if z < k { z } else { k };

                // 可达状态的最小值和最大值
                let min_next = z + k - 2 * max_x;
                let max_next = z + k - 2 * min_x;

                // 从对应奇偶性的集合中取出所有可达状态
                let parity = min_next & 1;
                let set = &mut unvisited[parity];

                // 收集需要移除的元素
                let mut to_remove = Vec::new();
                for &val in set.range((Bound::Included(min_next), Bound::Included(max_next))) {
                    to_remove.push(val);
                }

                // 移除并加入下一层队列
                for val in to_remove {
                    set.remove(&val);
                    next_queue.push_back(val);
                }
            }

            queue = next_queue;
            steps += 1;
        }

        -1
    }
}
```
