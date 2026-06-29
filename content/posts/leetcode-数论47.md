---
title: "leetcode-数论47"
date: 2026-06-20T11:09:56+08:00
tags: ["leetcode", "数论"]
draft: false
---


## 找出最大的 N 位 K 回文数

给你两个 正整数 n 和 k。

如果整数 x 满足以下全部条件，则该整数是一个 k 回文数：

x 是一个 回文数。

x 可以被 k 整除。

以字符串形式返回 最大的  n 位 k 回文数。

注意，该整数 不 含前导零。


```
impl Solution {
    pub fn largest_palindrome(n: i32, k: i32) -> String {
        let n = n as usize;
        let k = k as usize;

        // 预计算 10^i mod k，用于快速计算余数
        let pow10: Vec<usize> = (0..n)
            .scan(1, |state, _| {
                let val = *state;
                *state = (*state * 10) % k;
                Some(val)
            })
            .collect();

        let half_len = (n + 1) / 2; // 需要枚举的左半部分长度
        let mut ans = vec![0u8; n];

        // 记忆化：vis[pos][remainder] 表示该状态是否已访问
        let mut visited = vec![vec![false; k]; half_len + 1];

        /// 深度优先搜索构造回文数
        /// - pos: 当前正在填充的位置（从左到右）
        /// - remainder: 当前构造的数字对 k 的余数
        /// 返回：是否能成功构造一个合法的 k 回文数
        fn dfs(
            pos: usize,
            remainder: usize,
            n: usize,
            k: usize,
            half_len: usize,
            pow10: &[usize],
            ans: &mut [u8],
            visited: &mut [Vec<bool>],
        ) -> bool {
            // 已经构造完左半部分，检查余数是否为 0
            if pos == half_len {
                return remainder == 0;
            }

            // 标记当前状态已访问
            visited[pos][remainder] = true;

            // 贪心：从大到小尝试数字 9..=0
            for digit in (0..=9).rev() {
                // 计算加上当前数字后的新余数
                let new_remainder = if n % 2 == 1 && pos == half_len - 1 {
                    // 奇数长度：正中间位置，只贡献一次
                    (remainder + digit * pow10[pos]) % k
                } else {
                    // 偶数长度或非中间位置：左右对称，贡献两次
                    (remainder + digit * (pow10[pos] + pow10[n - 1 - pos])) % k
                };

                // 如果该状态未访问且能成功构造，则填入当前数字
                if !visited[pos + 1][new_remainder]
                    && dfs(pos + 1, new_remainder, n, k, half_len, pow10, ans, visited)
                {
                    ans[pos] = b'0' + digit as u8;
                    ans[n - 1 - pos] = b'0' + digit as u8;
                    return true;
                }
            }

            false
        }

        dfs(0, 0, n, k, half_len, &pow10, &mut ans, &mut visited);

        String::from_utf8(ans).unwrap()
    }
}
```
