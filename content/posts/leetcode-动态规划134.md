---
title: "leetcode-动态规划134"
date: 2026-10-05T20:40:03+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 找出有效子序列的最大长度 I

给你一个整数数组 nums。

nums 的子序列 sub 的长度为 x ，如果其满足以下条件，则称其为 有效子序列：

(sub[0] + sub[1]) % 2 == (sub[1] + sub[2]) % 2 == ... == (sub[x - 2] + sub[x - 1]) % 2

返回 nums 的 最长的有效子序列 的长度。


```
impl Solution {
    pub fn maximum_length(nums: Vec<i32>) -> i32 {
        // 有效子序列的相邻两数之和奇偶性必须恒定，因此子序列的奇偶模式
        // 只能是以下四种之一：(偶,偶)、(偶,奇)、(奇,偶)、(奇,奇)。
        // 对每种模式贪心扫描，统计能匹配该模式的最长子序列长度。
        let mut ans = 0i32;

        for first in 0..2 {
            for second in 0..2 {
                let pattern = [first, second];
                let mut len = 0i32;

                for &x in &nums {
                    if (x & 1) == pattern[(len & 1) as usize] {
                        len += 1;
                    }
                }

                ans = ans.max(len);
            }
        }

        ans
    }
}
```
