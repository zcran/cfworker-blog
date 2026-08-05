---
title: "leetcode-计数60"
date: 2026-08-01T10:40:54+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 数组中紧跟 key 之后出现最频繁的数字

给你一个下标从 0 开始的整数数组 nums ，同时给你一个整数 key ，它在 nums 出现过。

统计 在 nums 数组中紧跟着 key 后面出现的不同整数 target 的出现次数。换言之，target 的出现次数为满足以下条件的 i 的数目：

0 <= i <= n - 2
nums[i] == key 且
nums[i + 1] == target 。

请你返回出现 最多 次数的 target 。测试数据保证出现次数最多的 target 是唯一的。


```
impl Solution {
    pub fn most_frequent(nums: Vec<i32>, key: i32) -> i32 {
        // 根据题目约束，nums[i] <= 1000，可以用数组统计
        let mut freq = [0; 1001];
        let mut max_count = 0;
        let mut ans = 0;

        // 遍历每对相邻元素，key 后面的数字即为 target
        for i in 0..nums.len() - 1 {
            if nums[i] == key {
                let target = nums[i + 1] as usize;
                freq[target] += 1;

                // 更新出现次数最多的 target
                if freq[target] > max_count {
                    max_count = freq[target];
                    ans = target as i32;
                }
            }
        }

        ans
    }
}
```
