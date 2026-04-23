---
title: "leetcode-滚动哈希12"
date: 2026-04-17T20:00:31+08:00
tags: ["leetcode", "滚动哈希"]
draft: false
---


## 构造字符串的总得分和

你需要从空字符串开始 构造 一个长度为 n 的字符串 s ，构造的过程为每次给当前字符串 前面 添加 一个 字符。构造过程中得到的所有字符串编号为 1 到 n ，其中长度为 i 的字符串编号为 si 。

比方说，s = "abaca" ，s1 == "a" ，s2 == "ca" ，s3 == "aca" 依次类推。
si 的 得分 为 si 和 sn 的 最长公共前缀 的长度（注意 s == sn ）。

给你最终的字符串 s ，请你返回每一个 si 的 得分之和 。


```
impl Solution {
    /// 计算字符串 s 的所有后缀与整个字符串的最长公共前缀长度之和。
    /// 使用 Z 算法（Z-function）实现 O(n) 时间复杂度。
    pub fn sum_scores(s: String) -> i64 {
        let s = s.as_bytes();          // 转为字节数组，便于索引和比较
        let n = s.len();

        // Z 数组：z[i] 表示 s[i..] 与 s 的最长公共前缀长度（z[0] 通常为 0 或 n，这里按标准算法 z[0]=0）
        let mut z = vec![0; n];
        let mut l = 0;                 // 当前 Z-box 的左边界
        let mut r = 0;                 // 当前 Z-box 的右边界（包含）

        // 从索引 1 开始计算（z[0] 不参与累加，但 ans 初始已加 n）
        for i in 1..n {
            // 如果 i 在当前 Z-box 内，利用对称性初始化 z[i]
            if i <= r {
                // 对称位置的值，但不能超过 r-i+1（即剩余 box 长度）
                z[i] = std::cmp::min(z[i - l], r - i + 1);
            }
            // 继续暴力扩展，直到不匹配或到达边界
            while i + z[i] < n && s[z[i]] == s[i + z[i]] {
                // 扩展时更新 Z-box 边界
                l = i;
                r = i + z[i];
                z[i] += 1;
            }
        }

        // 初始 ans = n（每个后缀至少包含自身，即长度为 n 的公共前缀？实际上 z[0] 应为 n，但标准算法中 z[0]=0，
        // 这里加 n 相当于补上了自身匹配）
        let mut ans = n as i64;
        // 累加所有 z[i]（i >= 1）
        ans += z.iter().skip(1).map(|&v| v as i64).sum::<i64>();
        ans
    }
}
```
