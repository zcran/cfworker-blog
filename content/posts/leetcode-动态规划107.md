---
title: "leetcode-动态规划107"
date: 2026-10-05T20:40:01+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 统计特殊整数

如果一个正整数每一个数位都是 互不相同 的，我们称它是 特殊整数 。

给你一个 正 整数 n ，请你返回区间 [1, n] 之间特殊整数的数目。


```
impl Solution {
    pub fn count_special_numbers(n: i32) -> i32 {
        let digits: Vec<u8> = n.to_string().bytes().map(|b| b - b'0').collect();
        let len = digits.len();

        // 统计长度小于 len 的特殊整数数量
        // 长度 L 的数量：首位 9 种选择，之后每位依次 9, 8, 7, ... 递减
        let mut ans = 0;
        let mut prod = 9;
        for i in 0..len - 1 {
            ans += prod;
            prod *= 9 - i as i32;
        }

        // 记忆化：memo[mask] 记录在「当前已用数字集合为 mask」时，后续位的方案数
        // 由于 dp 只依赖 mask 和是否贴紧上界，可用数组替代哈希表
        let mut memo = vec![-1i32; 1 << 10];

        // 从最高位开始按位填数，tight 表示前面是否完全贴着 n 的前缀
        fn dfs(pos: usize, mask: usize, tight: bool, digits: &[u8], memo: &mut [i32]) -> i32 {
            if pos == digits.len() {
                return 1; // 填满所有位，得到一个特殊整数
            }
            // 只有在不贴紧上界时才能复用记忆化结果
            if !tight && memo[mask] != -1 {
                return memo[mask];
            }

            let upper = if tight { digits[pos] as usize } else { 9 };
            // 首位不能为 0（除非该位就是唯一一位，但此处首位指最高位）
            let lower = if pos == 0 { 1 } else { 0 };

            let mut count = 0;
            for d in lower..=upper {
                if mask >> d & 1 == 0 {
                    count += dfs(
                        pos + 1,
                        mask | (1 << d),
                        tight && d == upper,
                        digits,
                        memo,
                    );
                }
            }

            if !tight {
                memo[mask] = count;
            }
            count
        }

        ans + dfs(0, 0, true, &digits, &mut memo)
    }
}
```
