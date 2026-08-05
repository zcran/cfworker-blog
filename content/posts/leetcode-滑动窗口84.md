---
title: "leetcode-滑动窗口84"
date: 2026-07-18T11:02:34+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 几乎唯一子数组的最大和

给你一个整数数组 nums 和两个正整数 m 和 k 。

请你返回 nums 中长度为 k 的 几乎唯一 子数组的 最大和 ，如果不存在几乎唯一子数组，请你返回 0 。

如果 nums 的一个子数组有至少 m 个互不相同的元素，我们称它是 几乎唯一 子数组。

子数组指的是一个数组中一段连续 非空 的元素序列。


```
use std::collections::HashMap;

impl Solution {
    pub fn max_sum(nums: Vec<i32>, m: i32, k: i32) -> i64 {
        let (m, k) = (m as usize, k as usize);
        let mut ans = 0i64;
        let mut sum = 0i64;
        let mut freq = HashMap::with_capacity(k);

        for (i, &x) in nums.iter().enumerate() {
            // 1. 扩展窗口：添加右端元素
            sum += x as i64;
            *freq.entry(x).or_insert(0) += 1;

            // 窗口未满，继续扩展
            if i < k - 1 {
                continue;
            }

            // 2. 检查并更新答案
            if freq.len() >= m {
                ans = ans.max(sum);
            }

            // 3. 收缩窗口：移除左端元素
            let left = nums[i - k + 1];
            sum -= left as i64;
            match freq.get_mut(&left) {
                Some(count) => {
                    *count -= 1;
                    if *count == 0 {
                        freq.remove(&left);
                    }
                }
                None => unreachable!(),
            }
        }

        ans
    }
}
```
