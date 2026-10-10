---
title: "leetcode-动态规划142"
date: 2026-10-05T20:40:03+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 最小数组和

给你一个整数数组 nums 和三个整数 k、op1 和 op2。

你可以对 nums 执行以下操作：

操作 1：选择一个下标 i，将 nums[i] 除以 2，并 向上取整 到最接近的整数。你最多可以执行此操作 op1 次，并且每个下标最多只能执行一次。

操作 2：选择一个下标 i，仅当 nums[i] 大于或等于 k 时，从 nums[i] 中减去 k。你最多可以执行此操作 op2 次，并且每个下标最多只能执行一次。

注意： 两种操作可以应用于同一下标，但每种操作最多只能应用一次。

返回在执行任意次数的操作后，nums 中所有元素的 最小 可能 和 。


```
impl Solution {
    pub fn min_array_sum(nums: Vec<i32>, k: i32, op1: i32, op2: i32) -> i32 {
        let op1 = op1 as usize;
        let op2 = op2 as usize;

        // f[p][q]：使用 p 次操作1、q 次操作2 后，当前已处理元素的最小和
        let mut f = vec![vec![0i32; op2 + 1]; op1 + 1];

        for &x in &nums {
            // 倒序遍历，保证每个元素每种操作至多使用一次
            for p in (0..=op1).rev() {
                for q in (0..=op2).rev() {
                    // 不使用任何操作
                    let mut res = f[p][q] + x;

                    // 只使用操作1（除以2向上取整）
                    if p > 0 {
                        res = res.min(f[p - 1][q] + (x + 1) / 2);
                    }

                    // 只使用操作2（减去 k）
                    if q > 0 && x >= k {
                        res = res.min(f[p][q - 1] + x - k);

                        // 同时使用操作1和操作2
                        if p > 0 {
                            let half = (x + 1) / 2;
                            // 两种顺序取更优：先除后减 / 先减后除
                            let y = if half >= k {
                                half - k
                            } else {
                                (x - k + 1) / 2
                            };
                            res = res.min(f[p - 1][q - 1] + y);
                        }
                    }

                    f[p][q] = res;
                }
            }
        }

        f[op1][op2]
    }
}
```
