---
title: "leetcode-位运算9"
date: 2026-09-24T10:33:34+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 比特位计数

给你一个整数 n ，对于 0 <= i <= n 中的每个 i ，计算其二进制表示中 1 的个数 ，返回一个长度为 n + 1 的数组 ans 作为答案。

不要使用内置函数来解决（例如，C++ 中的 __builtin_popcount）。


```
impl Solution {
    /// 核心思路（动态规划）：
    /// 对于任意 i，有递推关系：
    ///     bits(i) = bits(i >> 1) + (i & 1)
    /// 即 i 的 1 的个数 = 去掉最低位后的 1 的个数 + 最低位本身。
    ///
    /// 由于 i >> 1 < i，可以从小到大依次计算，避免重复统计。
    ///
    /// 时间复杂度 O(n)，空间复杂度 O(n)（仅用于存储结果）。
    pub fn count_bits(n: i32) -> Vec<i32> {
        let n = n as usize;

        // 预分配空间，避免动态扩容
        let mut ans = Vec::with_capacity(n + 1);
        ans.push(0);

        for i in 1..=n {
            // 复用已计算好的 ans[i >> 1]，加上最低位
            ans.push(ans[i >> 1] + (i & 1) as i32);
        }

        ans
    }
}
```
