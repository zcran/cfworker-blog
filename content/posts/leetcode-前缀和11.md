---
title: "leetcode-前缀和11"
date: 2026-08-25T10:02:46+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## DI 序列的有效排列

给定一个长度为 n 的字符串 s ，其中 s[i] 是:

“D” 意味着减少，或者
“I” 意味着增加

有效排列 是对有 n + 1 个在 [0, n]  范围内的整数的一个排列 perm ，使得对所有的 i：

如果 s[i] == 'D'，那么 perm[i] > perm[i+1]，以及；
如果 s[i] == 'I'，那么 perm[i] < perm[i+1]。

返回 有效排列  perm的数量 。因为答案可能很大，所以请返回你的答案对 10^9 + 7 取余。


```
impl Solution {
    pub fn num_perms_di_sequence(s: String) -> i32 {
        const MOD: i32 = 1_000_000_007;
        let n = s.len();
        let s = s.as_bytes();

        // dp[i][j] 表示长度为 i+1 的排列中，以数字 j 结尾的有效排列数
        // 使用滚动数组优化空间，只保留当前行和上一行
        let mut dp = vec![0; n + 1];
        let mut prev = vec![0; n + 1];

        // 长度为 1 的排列只有 [0]，以 0 结尾
        prev[0] = 1;

        for i in 1..=n {
            // 当前行清零
            dp.fill(0);

            if s[i - 1] == b'D' {
                // 对于 'D'：perm[i-1] > perm[i]
                // 如果当前末尾是 j，则上一个末尾必须 >= j
                // 从后向前累加，dp[j] = prev[j] + prev[j+1] + ... + prev[i-1]
                let mut sum = 0;
                for j in (0..i).rev() {
                    sum = (sum + prev[j]) % MOD;
                    dp[j] = sum;
                }
            } else {
                // 对于 'I'：perm[i-1] < perm[i]
                // 如果当前末尾是 j，则上一个末尾必须 < j
                // 从前向后累加，dp[j] = prev[0] + prev[1] + ... + prev[j-1]
                let mut sum = 0;
                for j in 0..=i {
                    dp[j] = sum;
                    if j < i {
                        sum = (sum + prev[j]) % MOD;
                    }
                }
            }

            // 交换当前行和上一行
            std::mem::swap(&mut dp, &mut prev);
        }

        // 总数为所有可能结尾的数字之和
        let mut result = 0;
        for &val in &prev[..=n] {
            result = (result + val) % MOD;
        }
        result
    }
}
```
