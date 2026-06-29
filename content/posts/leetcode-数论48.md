---
title: "leetcode-数论48"
date: 2026-06-20T11:09:56+08:00
tags: ["leetcode", "数论"]
draft: false
---


## 使数组非递减的最少除法操作次数

给你一个整数数组 nums 。

一个正整数 x 的任何一个 严格小于 x 的 正 因子都被称为 x 的 真因数 。比方说 2 是 4 的 真因数，但 6 不是 6 的 真因数。

你可以对 nums 的任何数字做任意次 操作 ，一次 操作 中，你可以选择 nums 中的任意一个元素，将它除以它的 最大真因数 。

你的目标是将数组变为 非递减 的，请你返回达成这一目标需要的 最少操作 次数。

如果 无法 将数组变成非递减的，请你返回 -1 。


```
use std::sync::OnceLock;
impl Solution {
    // 预计算所有数的最小质因子（最大真因数）
    // 使用 OnceLock 实现懒加载，只在第一次调用时初始化
    pub fn min_operations(mut nums: Vec<i32>) -> i32 {
        // 静态缓存，使用 OnceLock 确保只初始化一次
        static LPF_CACHE: OnceLock<[i32; 1_000_001]> = OnceLock::new();

        // 获取或初始化缓存
        let cache = LPF_CACHE.get_or_init(|| {
            let mut lpf = [0; 1_000_001];

            // 埃氏筛计算每个数的最小质因子
            for i in 2..=1_000_000 {
                if lpf[i] == 0 {
                    // i 是质数，标记所有 i 的倍数
                    let mut j = i;
                    while j <= 1_000_000 {
                        if lpf[j] == 0 {
                            lpf[j] = i as i32;
                        }
                        j += i;
                    }
                }
            }

            // 质数的最小质因子是它本身
            for i in 2..=1_000_000 {
                if lpf[i] == 0 {
                    lpf[i] = i as i32;
                }
            }

            lpf
        });

        // 辅助函数：获取数字的最小质因子
        let get_min_prime_factor = |x: i32| -> i32 {
            cache[x as usize]
        };

        let mut operations = 0;

        // 从右向左遍历，保持 nums[i] <= nums[i+1]
        for i in (0..nums.len() - 1).rev() {
            // 如果当前元素大于后一个元素，需要操作
            if nums[i] > nums[i + 1] {
                let min_prime = get_min_prime_factor(nums[i]);

                // 如果最小质因子等于原数（即 nums[i] 是质数），无法变小
                if min_prime == nums[i] {
                    return -1;
                }

                nums[i] = min_prime;
                operations += 1;

                // 如果替换后仍然大于后一个元素，无法使数组非递减
                if nums[i] > nums[i + 1] {
                    return -1;
                }
            }
        }

        operations
    }
}
```
