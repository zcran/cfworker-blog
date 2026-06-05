---
title: "leetcode-组合数学15"
date: 2026-05-24T09:54:13+08:00
tags: ["leetcode", "组合数学"]
draft: false
---


## 数组的三角和


给你一个下标从 0 开始的整数数组 nums ，其中 nums[i] 是 0 到 9 之间（两者都包含）的一个数字。

nums 的 三角和 是执行以下操作以后最后剩下元素的值：

1.nums 初始包含 n 个元素。如果 n == 1 ，终止 操作。否则，创建 一个新的下标从 0 开始的长度为 n - 1 的整数数组 newNums 。
2.对于满足 0 <= i < n - 1 的下标 i ，newNums[i] 赋值 为 (nums[i] + nums[i+1]) % 10 ，% 表示取余运算。
3.将 newNums 替换 数组 nums 。
4.从步骤 1 开始 重复 整个过程。

请你返回 nums 的三角和。


```
impl Solution {
    pub fn triangular_sum(mut nums: Vec<i32>) -> i32 {
        // 使用双缓冲技术，避免重复分配内存
        let mut dst = Vec::with_capacity(nums.len() - 1);

        while nums.len() > 1 {
            dst.clear();                     // 清空但保留容量
            // 预分配恰好所需容量（避免多次扩容）
            dst.reserve(nums.len() - 1);

            for i in 0..nums.len() - 1 {
                // 相邻元素求和后取个位数
                dst.push((nums[i] + nums[i + 1]) % 10);
            }

            // 交换源和目标缓冲区，复用内存
            std::mem::swap(&mut nums, &mut dst);
        }

        nums[0]
    }
}
```
