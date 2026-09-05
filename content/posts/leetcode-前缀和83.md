---
title: "leetcode-前缀和83"
date: 2026-08-25T10:02:51+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 等值距离和

给你一个下标从 0 开始的整数数组 nums。

现有一个长度等于 nums.length 的数组 arr 。对于满足 nums[j] == nums[i] 且 j != i 的所有 j ，arr[i] 等于所有 |i - j| 之和。如果不存在这样的 j ，则令 arr[i] 等于 0 。

返回数组 arr 。

```
use std::collections::HashMap;

impl Solution {
    pub fn distance(nums: Vec<i32>) -> Vec<i64> {
        // 按值分组，存储每个值出现的索引
        let mut groups: HashMap<i32, Vec<usize>> = HashMap::new();
        for (i, &num) in nums.iter().enumerate() {
            groups.entry(num).or_insert_with(Vec::new).push(i);
        }

        let mut ans = vec![0i64; nums.len()];

        // 处理每个分组
        for indices in groups.values() {
            let m = indices.len();
            if m <= 1 {
                continue; // 单个元素或空组，距离为0
            }

            // 计算总距离
            let total_sum: i64 = indices.iter().map(|&idx| idx as i64).sum();
            let mut left_sum = 0i64;

            for (i, &idx) in indices.iter().enumerate() {
                let idx = idx as i64;
                let left_count = i as i64;
                let right_count = (m - i - 1) as i64;

                // 公式：左边所有元素到 idx 的距离和 + 右边所有元素到 idx 的距离和
                // = idx * left_count - left_sum + (total_sum - left_sum - idx) - idx * right_count
                ans[idx as usize] = idx * left_count - left_sum
                                   + (total_sum - left_sum - idx) - idx * right_count;

                left_sum += idx;
            }
        }

        ans
    }
}
```
