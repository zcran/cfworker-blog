---
title: "leetcode-枚举14"
date: 2026-07-09T10:05:00+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 向下取整数对和

给你一个整数数组 nums ，请你返回所有下标对 0 <= i, j < nums.length 的 floor(nums[i] / nums[j]) 结果之和。由于答案可能会很大，请你返回答案对109 + 7 取余 的结果。

函数 floor() 返回输入数字的整数部分。


```
impl Solution {
    /// 计算所有 floor(nums[i] / nums[j]) 的和，结果对 1e9+7 取模。
    ///
    /// 思路：统计每个值的出现次数，利用前缀和快速计算每个除数对商数的贡献。
    /// 对于每个值 x 作为除数，商为 k 时，被除数范围是 [k*x, (k+1)*x - 1]。
    pub fn sum_of_floored_pairs(nums: Vec<i32>) -> i32 {
        const MOD: i64 = 1_000_000_007;

        let max_val = *nums.iter().max().unwrap() as usize;

        // 统计每个数字的出现次数
        let mut freq = vec![0; max_val + 1];
        for &num in &nums {
            freq[num as usize] += 1;
        }

        // 构建前缀和，prefix[i] 表示值 <= i 的元素个数
        let mut prefix = freq;
        for i in 1..=max_val {
            prefix[i] += prefix[i - 1];
        }

        let mut result = 0i64;

        // 枚举除数 x
        for x in 1..=max_val {
            let count_x = prefix[x] - prefix[x - 1];
            if count_x == 0 {
                continue;
            }

            // 枚举商 k = floor(y / x)，y 是被除数
            let mut k = 1;
            let mut left = x; // 被除数下界
            while left <= max_val {
                let right = (x * (k + 1) - 1).min(max_val); // 被除数上界
                let count_y = prefix[right] - prefix[left - 1];

                if count_y > 0 {
                    // x 作为除数时，贡献 = count_x * count_y * k
                    let contribution = (count_x as i64) * (count_y as i64) % MOD * (k as i64) % MOD;
                    result = (result + contribution) % MOD;
                }

                k += 1;
                left = x * k;
            }
        }

        result as i32
    }
}
```
