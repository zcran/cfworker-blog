---
title: "leetcode-位运算76"
date: 2026-09-24T10:33:39+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 统计二进制回文数字的数目

给你一个 非负 整数 n。

如果一个 非负 整数的二进制表示（不含前导零）正着读和倒着读都一样，则称该数为 二进制回文数。

返回满足 0 <= k <= n 且 k 的二进制表示是回文数的整数 k 的数量。

注意： 数字 0 被认为是二进制回文数，其表示为 "0"。


```
impl Solution {
    /// 统计 [0, n] 中二进制表示为回文数的整数个数。
    ///
    /// 思路：
    /// 1. 设 n 的二进制长度为 m。所有长度 < m 的二进制回文数全部计入。
    /// 2. 对长度 = m 的回文数，只需确定其前半部分（含中间位），后半部分由对称性唯一确定。
    /// 3. 用 n 的高位部分作为基准，构造"恰好不超过 n"的最大回文数并比较。
    pub fn count_binary_palindromes(n: i64) -> i32 {
        // 特判：0 和 1
        if n == 0 {
            return 1;
        }
        if n == 1 {
            return 2;
        }

        let n = n as u64;
        let m = 64 - n.leading_zeros();           // n 的二进制长度
        let half = (m / 2) as u32;                 // 回文数后半部分长度

        // 长度 < m 的所有二进制回文数：
        // 长度 1：1 个（"0" 单独处理）；长度 L >= 2 时，回文数个数 = 2^((L-1)/2)
        // 累计为 2^ceil(m/2) - 1（m >= 2 时），即长度 1..m-1 的全部回文数
        let k = (m - 1) / 2;                       // 长度 m 时前半部分可自由变化的位数
        let mut ans = (1u64 << (k + 1)) - 1;       // 长度 1..m-1 的回文总数
        if m % 2 == 0 {
            ans += 1u64 << k;                      // 长度 m 但首尾对称约束下的额外一组
        }

        // 长度 = m 的回文数：取 n 的高半部分 left 作为前缀
        let left = (n >> half) as u64;             // 高 half 或 half+1 位
        ans += left - (1u64 << k);                 // left 从 2^k 起计数，超过基准的计入

        // 由 left 对称构造回文数，检查是否 <= n
        let mut right = if m % 2 == 0 { left } else { left >> 1 };
        let mut reversed = 0u64;
        for _ in 0..half {
            reversed = (reversed << 1) | (right & 1);
            right >>= 1;
        }
        let constructed = (left << half) | reversed;
        if constructed <= n {
            ans += 1;
        }

        ans as i32
    }
}
```
