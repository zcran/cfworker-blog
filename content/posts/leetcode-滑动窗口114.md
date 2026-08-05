---
title: "leetcode-滑动窗口114"
date: 2026-07-18T11:02:35+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 统计包含 K 个不同整数的子数组

给你一个整数数组 nums 和两个整数 k 和 m。

返回一个整数，表示满足以下条件的 子数组 的数量：

子数组 恰好 包含 k 个不同的 整数。

在子数组中，每个 不同的 整数 至少 出现 m 次。

子数组 是数组中一个连续的、非空 元素序列。


```
use std::collections::HashMap;

impl Solution {
    pub fn count_subarrays(nums: Vec<i32>, k: i32, m: i32) -> i64 {
        let k = k as usize;
        let m = m as usize;
        let n = nums.len();

        // 计算「不同整数的个数 >= limit 且所有不同整数都至少出现 m 次」的子数组数量
        let calc = |limit: usize| -> i64 {
            let mut cnt = HashMap::new();   // 窗口内每个整数的出现次数
            let mut ge_m = 0;               // 窗口内出现次数 >= m 的不同整数个数
            let mut left = 0;               // 窗口左边界
            let mut ans = 0i64;

            for &x in &nums {
                // 1. 扩展右边界
                let entry = cnt.entry(x).or_insert(0);
                *entry += 1;
                if *entry == m {
                    ge_m += 1;
                }

                // 2. 收缩左边界：当窗口满足「不同整数数 >= limit 且 ge_m >= k」时，
                //    说明当前窗口以及任何更大的窗口都满足条件，但为了计数，我们尽量缩小窗口。
                while cnt.len() >= limit && ge_m >= k {
                    let out = nums[left];
                    let out_cnt = cnt.get_mut(&out).unwrap();
                    if *out_cnt == m {
                        ge_m -= 1;
                    }
                    *out_cnt -= 1;
                    if *out_cnt == 0 {
                        cnt.remove(&out);
                    }
                    left += 1;
                }

                // 3. 此时左边界 left 表示有多少个起始位置（0..left-1）与当前右边界组成的子数组满足条件
                ans += left as i64;
            }

            ans
        };

        // 容斥：恰好 k 个不同整数 = (至少 k 个) - (至少 k+1 个)
        calc(k) - calc(k + 1)
    }
}
```
