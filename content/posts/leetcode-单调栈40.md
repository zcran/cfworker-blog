---
title: "leetcode-单调栈40"
date: 2026-06-07T10:14:05+08:00
tags: ["leetcode", "单调栈"]
draft: false
---


## 操作使得分最大

给你一个长度为 n 的正整数数组 nums 和一个整数 k 。

一开始，你的分数为 1 。你可以进行以下操作至多 k 次，目标是使你的分数最大：

· 选择一个之前没有选过的 非空 子数组 nums[l, ..., r] 。
· 从 nums[l, ..., r] 里面选择一个 质数分数 最高的元素 x 。如果多个元素质数分数相同且最高，选择下标最小的一个。
· 将你的分数乘以 x 。

nums[l, ..., r] 表示 nums 中起始下标为 l ，结束下标为 r 的子数组，两个端点都包含。

一个整数的 质数分数 等于 x 不同质因子的数目。比方说， 300 的质数分数为 3 ，因为 300 = 2 * 2 * 3 * 5 * 5 。

请你返回进行至多 k 次操作后，可以得到的 最大分数 。

由于答案可能很大，请你将结果对 10^9 + 7 取余后返回。

```
impl Solution {
    const MOD: i64 = 1_000_000_007;
    const MX: usize = 100_001;

    /// 获取质数分数数组（使用静态局部变量）
    fn get_omega() -> &'static Vec<i32> {
        use std::sync::OnceLock;
        static OMEGA: OnceLock<Vec<i32>> = OnceLock::new();
        OMEGA.get_or_init(|| {
            let mut omega = vec![0; Self::MX];
            for i in 2..Self::MX {
                if omega[i] == 0 {
                    for j in (i..Self::MX).step_by(i) {
                        omega[j] += 1;
                    }
                }
            }
            omega
        })
    }

    fn pow(mut x: i64, mut n: i32) -> i64 {
        let mut res = 1;
        while n > 0 {
            if n & 1 == 1 {
                res = (res * x) % Self::MOD;
            }
            x = (x * x) % Self::MOD;
            n >>= 1;
        }
        res
    }

    pub fn maximum_score(nums: Vec<i32>, mut k: i32) -> i32 {
        let omega = Self::get_omega();
        let n = nums.len();
        let nums_i64: Vec<i64> = nums.iter().map(|&x| x as i64).collect();

        // 单调栈求左右边界
        let mut left = vec![-1i32; n];
        let mut right = vec![n as i32; n];
        let mut stack = Vec::with_capacity(n);

        for i in 0..n {
            let score_i = omega[nums[i] as usize];
            while let Some(&top) = stack.last() {
                if omega[nums[top as usize] as usize] < score_i {
                    right[top as usize] = i as i32;
                    stack.pop();
                } else {
                    break;
                }
            }
            left[i] = *stack.last().unwrap_or(&-1);
            stack.push(i as i32);
        }

        // 按值降序排序
        let mut indices: Vec<usize> = (0..n).collect();
        indices.sort_unstable_by(|&a, &b| nums[b].cmp(&nums[a]));

        // 贪心计算结果
        let mut ans = 1i64;
        let mut remaining = k as i64;

        for &idx in &indices {
            let cnt = (idx as i32 - left[idx]) as i64 * (right[idx] - idx as i32) as i64;

            if cnt >= remaining {
                ans = (ans * Self::pow(nums_i64[idx], remaining as i32)) % Self::MOD;
                break;
            } else {
                ans = (ans * Self::pow(nums_i64[idx], cnt as i32)) % Self::MOD;
                remaining -= cnt;
            }
        }

        ans as i32
    }
}
```
