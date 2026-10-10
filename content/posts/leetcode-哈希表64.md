---
title: "leetcode-哈希表64"
date: 2026-10-09T20:07:59+08:00
tags: ["leetcode", "哈希表"]
draft: false
---


## 数组中的最大数对和

给你一个下标从 0 开始的整数数组 nums 。请你从 nums 中找出和 最大 的一对数，且这两个数数位上最大的数字相等。

返回最大和，如果不存在满足题意的数字对，返回 -1 。


```
impl Solution {
    pub fn max_sum(nums: Vec<i32>) -> i32 {
        // best[d] = 数位最大数字为 d 的数中，目前遇到的最大值
        // 初始为 -1，表示该类别尚未出现
        let mut best = [-1i32; 10];
        let mut ans = -1;

        for v in nums {
            // 求 v 的数位最大数字
            let mut max_digit = 0;
            let mut x = v;
            while x > 0 {
                max_digit = max_digit.max(x % 10);
                x /= 10;
            }

            let d = max_digit as usize;
            // 若该类别此前已有数，则可与当前数组成一对
            if best[d] != -1 {
                ans = ans.max(best[d] + v);
            }
            // 更新该类别最大值
            best[d] = best[d].max(v);
        }

        ans
    }
}
```
