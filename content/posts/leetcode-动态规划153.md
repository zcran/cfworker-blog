---
title: "leetcode-动态规划153"
date: 2026-10-05T20:40:04+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 求出数组的 X 值 I

给你一个由 正 整数组成的数组 nums，以及一个 正 整数 k。

你可以对 nums 执行 一次 操作，该操作中可以移除任意 不重叠 的前缀和后缀，使得 nums 仍然 非空 。

你需要找出 nums 的 x 值，即在执行操作后，剩余元素的 乘积 除以 k 后的 余数 为 x 的操作数量。

返回一个大小为 k 的数组 result，其中 result[x] 表示对于 0 <= x <= k - 1，nums 的 x 值。

数组的 前缀 指从数组起始位置开始到数组中任意位置的一段连续子数组。

数组的 后缀 是指从数组中任意位置开始到数组末尾的一段连续子数组。

子数组 是数组中一段连续的元素序列。

注意，在操作中选择的前缀和后缀可以是 空的 。


```
impl Solution {
    pub fn result_array(nums: Vec<i32>, k: i32) -> Vec<i64> {
        let k = k as usize;
        let mut result = vec![0i64; k];
        // dp[r] 表示以当前元素结尾、乘积模 k 为 r 的连续子数组个数
        let mut dp = vec![0i64; k];
        // ndp 用于暂存下一轮的状态，避免每轮重新分配内存
        let mut ndp = vec![0i64; k];

        for &x in &nums {
            let x_mod = (x as usize) % k;

            // 清空 ndp
            ndp.iter_mut().for_each(|v| *v = 0);

            // 仅包含当前元素的子数组 [x]
            ndp[x_mod] += 1;

            // 在之前以 x 前一个元素结尾的子数组后追加 x
            for r in 0..k {
                if dp[r] != 0 {
                    let nr = ((r as u64 * x_mod as u64) % k as u64) as usize;
                    ndp[nr] += dp[r];
                }
            }

            // 将当前元素结尾的所有子数组计入答案
            for r in 0..k {
                result[r] += ndp[r];
            }

            // 交换 dp 与 ndp，为下一轮迭代准备
            std::mem::swap(&mut dp, &mut ndp);
        }

        result
    }
}
```
