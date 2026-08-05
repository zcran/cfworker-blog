---
title: "leetcode-枚举98"
date: 2026-07-09T10:05:04+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 两个值之间的最小绝对差值

给你一个只包含 0、1 和 2 的整数数组 nums。

如果 nums[i] == 1 且 nums[j] == 2，则称下标对 (i, j) 为 有效 的。

请返回所有有效下标对中 i 和 j 之间的 最小 绝对差。如果不存在有效下标对，则返回 -1。

下标 i 和 j 之间的绝对差定义为 abs(i - j)。


```
impl Solution {
    pub fn min_absolute_difference(nums: Vec<i32>) -> i32 {
        let n = nums.len();
        let mut ans = n as i32;

        // 记录上一次出现 1 和 2 的位置
        // 初始化为 -n，保证差值为 n 时不会更新答案
        let mut last1 = -(n as i32);
        let mut last2 = -(n as i32);

        for (i, &x) in nums.iter().enumerate() {
            let i = i as i32;
            match x {
                1 => {
                    // 当前是 1，找上一个 2
                    ans = ans.min(i - last2);
                    last1 = i;
                }
                2 => {
                    // 当前是 2，找上一个 1
                    ans = ans.min(i - last1);
                    last2 = i;
                }
                _ => {} // 忽略 0
            }
        }

        if ans == n as i32 {
            -1
        } else {
            ans
        }
    }
}
```
