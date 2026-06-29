---
title: "leetcode-单调栈35"
date: 2026-06-07T10:14:05+08:00
tags: ["leetcode", "单调栈"]
draft: false
---


## 巫师的总力量和

作为国王的统治者，你有一支巫师军队听你指挥。

给你一个下标从 0 开始的整数数组 strength ，其中 strength[i] 表示第 i 位巫师的力量值。对于连续的一组巫师（也就是这些巫师的力量值是 strength 的 子数组），总力量 定义为以下两个值的 乘积 ：

· 巫师中 最弱 的能力值。
· 组中所有巫师的个人力量值 之和 。

请你返回 所有 巫师组的 总 力量之和。由于答案可能很大，请将答案对 109 + 7 取余 后返回。

子数组 是一个数组里 非空 连续子序列。

```
impl Solution {
    pub fn total_strength(strength: Vec<i32>) -> i32 {
        const MOD: i64 = 1_000_000_007;
        let n = strength.len();

        // 1. 单调栈找到每个元素作为最小值的作用范围
        let mut left = vec![-1i32; n];
        let mut right = vec![n as i32; n];
        let mut stack: Vec<i32> = Vec::new();

        for i in 0..n {
            // 右侧找小于等于（为了去重）
            while let Some(&top) = stack.last() {
                if strength[top as usize] >= strength[i] {
                    right[top as usize] = i as i32;
                    stack.pop();
                } else {
                    break;
                }
}
            // 左侧找严格小于
            left[i] = *stack.last().unwrap_or(&-1);
            stack.push(i as i32);
        }

        // 2. 计算前缀和数组
        let mut prefix_sum = vec![0i64; n + 1];
        for i in 0..n {
            prefix_sum[i + 1] = (prefix_sum[i] + strength[i] as i64) % MOD;
        }

        // 3. 计算前缀和的前缀和数组
        let mut prefix_sum_of_prefix = vec![0i64; n + 2];
        for i in 0..=n {
            prefix_sum_of_prefix[i + 1] = (prefix_sum_of_prefix[i] + prefix_sum[i]) % MOD;
        }

        // 4. 计算每个元素作为最小值时的贡献
        let mut ans = 0i64;

        for i in 0..n {
            let l = left[i] as usize + 1;  // 左边界（包含）
            let r = right[i] as usize - 1; // 右边界（包含）
            let val = strength[i] as i64;

            // 左边可能的子数组起点数量 = i - l + 1
            // 右边可能的子数组终点数量 = r - i + 1
            let left_count = (i - l + 1) as i64;
            let right_count = (r - i + 1) as i64;

            // 计算以 i 为最小值的所有子数组的和的总和
            // 公式: sum_{x=l}^{i} sum_{y=i}^{r} (prefix_sum[y+1] - prefix_sum[x])
            // = left_count * (prefix_sum_of_prefix[r+2] - prefix_sum_of_prefix[i+1])
            //   - right_count * (prefix_sum_of_prefix[i+1] - prefix_sum_of_prefix[l])

            let sum_right_part = (prefix_sum_of_prefix[r + 2] - prefix_sum_of_prefix[i + 1] + MOD) % MOD;
            let sum_left_part = (prefix_sum_of_prefix[i + 1] - prefix_sum_of_prefix[l] + MOD) % MOD;

            let total_sum = (left_count % MOD * sum_right_part % MOD - right_count % MOD * sum_left_part % MOD + MOD) % MOD;

            // 贡献 = 最小值 * 所有子数组和的总和
            let contribution = val % MOD * total_sum % MOD;
            ans = (ans + contribution) % MOD;
        }

        ans as i32
    }
}
```
