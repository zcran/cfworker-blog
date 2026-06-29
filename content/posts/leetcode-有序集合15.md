---
title: "leetcode-有序集合15"
date: 2026-06-14T10:47:41+08:00
tags: ["leetcode", "有序集合"]
draft: false
---


## 数组的最小偏移量

给你一个由 n 个正整数组成的数组 nums 。

你可以对数组的任意元素执行任意次数的两类操作：

· 如果元素是 偶数 ，除以 2
    · 例如，如果数组是 [1,2,3,4] ，那么你可以对最后一个元素执行此操作，使其变成 [1,2,3,2]
· 如果元素是 奇数 ，乘上 2
    · 例如，如果数组是 [1,2,3,4] ，那么你可以对第一个元素执行此操作，使其变成 [2,2,3,4]

数组的 偏移量 是数组中任意两个元素之间的 最大差值 。

返回数组在执行某些操作之后可以拥有的 最小偏移量 。


```
use std::collections::BinaryHeap;

impl Solution {
    /// 计算数组在经过操作后可以达到的最小偏移量
    ///
    /// 操作规则：
    /// - 偶数：除以2
    /// - 奇数：乘以2
    ///
    /// # 时间复杂度
    /// O(n log n)
    ///
    /// # 空间复杂度
    /// O(n)
    pub fn minimum_deviation(mut nums: Vec<i32>) -> i32 {
        // 1. 将所有奇数乘以2，统一为偶数
        // 这样后续只需要考虑除以2的操作
        for num in &mut nums {
            if *num & 1 == 1 {
                *num <<= 1;
            }
        }

        // 2. 构建最大堆
        let mut heap = BinaryHeap::from(nums);
        let mut min_val = *heap.iter().min().unwrap();
        let mut ans = heap.peek().unwrap() - min_val;

        // 3. 贪心优化：每次将最大的偶数减半
        while let Some(&max_val) = heap.peek() {
            // 如果最大值是奇数，无法再减半
            if max_val & 1 == 1 {
                break;
            }

            // 弹出最大值，减半后重新加入
            heap.pop();
            let new_val = max_val >> 1;
            heap.push(new_val);

            // 更新最小值和答案
            min_val = min_val.min(new_val);
            ans = ans.min(heap.peek().unwrap() - min_val);
        }

        ans
    }
}
```
