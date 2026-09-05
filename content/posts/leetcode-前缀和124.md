---
title: "leetcode-前缀和124"
date: 2026-08-25T10:02:53+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 统计有序数组中可被 K 整除的子数组数量

给你一个按 非降序 排列的整数数组 nums 和一个正整数 k。

如果 nums 的某个 子数组 的元素和可以被 k 整除，则称其为 良好 子数组。

返回一个整数，表示 nums 中 不同 的 良好 子数组的数量。

子数组 是数组中连续且 非空 的一段元素序列。

当两个子数组的数值序列不同，它们就被视为 不同 的子数组。例如，在 [1, 1, 1] 中，有 3 个 不同 的子数组，分别是 [1]、[1, 1] 和 [1, 1, 1]。


```
use std::collections::HashMap;

impl Solution {
    pub fn num_good_subarrays(nums: Vec<i32>, k: i32) -> i64 {
        let n = nums.len();
        let k = k as i64;

        // 统计所有和能被 k 整除的子数组（包括重复）
        let mut cnt = HashMap::new();
        let mut prefix = 0i64;
        let mut ans = 0i64;
        // 空前缀余数为 0
        cnt.insert(0, 1);

        for &x in &nums {
            prefix = (prefix + x as i64) % k;
            let count = cnt.get(&prefix).copied().unwrap_or(0);
            ans += count;
            *cnt.entry(prefix).or_insert(0) += 1;
        }

        // 减去重复的子数组
        let mut start = 0;
        for i in 0..n {
            let x = nums[i] as i64;
            // 找到连续相同元素段的末尾
            if i < n - 1 && nums[i] == nums[i + 1] {
                continue;
            }

            let len = (i - start + 1) as i64; // 当前段的长度
            // 枚举子数组长度 sz，如果 sz * x % k == 0，说明这段中所有长度为 sz 的子数组和都能被 k 整除
            for sz in 1..=len {
                if (x * sz) % k == 0 {
                    // 长度为 sz 的子数组共有 len - sz + 1 个
                    // 其中重复的有 len - sz 个（除了第一个，其余都和前一个重复）
                    ans -= len - sz;
                }
            }

            start = i + 1;
        }

        ans
    }
}
```
