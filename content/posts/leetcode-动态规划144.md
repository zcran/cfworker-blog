---
title: "leetcode-动态规划144"
date: 2026-10-05T20:40:04+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 最长相邻绝对差递减子序列

给你一个整数数组 nums 。

你的任务是找到 nums 中的 最长 子序列 seq ，这个子序列中相邻元素的 绝对差 构成一个 非递增 整数序列。换句话说，nums 中的序列 seqˇ0, seqˇ1, seqˇ2, ..., seqˇm 满足 |seqˇ1 - seqˇ0| >= |seqˇ2 - seqˇ1| >= ... >= |seqˇm - seqˇ(m - 1)| 。

请你返回这个子序列的长度。


```
impl Solution {
    pub fn longest_subsequence(nums: Vec<i32>) -> i32 {
        let mx = *nums.iter().max().unwrap() as usize;
        let min = *nums.iter().min().unwrap() as usize;
        let max_d = mx - min; // 最大可能绝对差

        // f[v][d]：以值 v 结尾、且最后一个相邻绝对差为 d 的最长子序列长度
        // 注意：d 是「当前元素与前一个元素的绝对差」
        let mut f = vec![vec![0i32; max_d + 1]; mx + 1];

        let mut ans = 0;

        for &x in &nums {
            let x = x as usize;
            let mut best = 1; // 至少可以单独成段

            // 从大到小枚举 d，保证同一元素不会被重复使用
            for d in (0..=max_d).rev() {
                // 前一个元素为 x - d，当前差为 d
                if x >= d {
                    let prev = f[x - d][d] + 1;
                    if prev > best {
                        best = prev;
                    }
                }
                // 前一个元素为 x + d，当前差为 d
                if x + d <= mx {
                    let prev = f[x + d][d] + 1;
                    if prev > best {
                        best = prev;
                    }
                }

                f[x][d] = best;
                if best > ans {
                    ans = best;
                }
            }
        }

        ans
    }
}
```
