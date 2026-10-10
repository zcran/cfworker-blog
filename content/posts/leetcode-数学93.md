---
title: "leetcode-数学93"
date: 2026-10-07T20:39:34+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 最大数字范围的整数之和

给你一个整数数组 nums。

一个整数的 数字范围 定义为其 最大 数字与 最小 数字之间的差。

例如，5724 的数字范围为 7 - 2 = 5。

返回 nums 中所有 数字范围 等于数组中 最大数字范围 的整数之和。


```
impl Solution {
    pub fn max_digit_range(nums: Vec<i32>) -> i32 {
        let mut max_range = -1; // 初始化为 -1，保证首个元素一定会触发重新累加
        let mut ans = 0;

        for &x in &nums {
            // 计算当前数字的各位最小值和最大值
            let mut mn = 9;
            let mut mx = 0;
            let mut v = x;
            while v > 0 {
                let d = v % 10;
                if d < mn { mn = d; }
                if d > mx { mx = d; }
                v /= 10;
            }

            let r = mx - mn;
            if r > max_range {
                max_range = r;
                ans = x; // 出现更大的数字范围，重置累加
            } else if r == max_range {
                ans += x;
            }
        }

        ans
    }
}
```
