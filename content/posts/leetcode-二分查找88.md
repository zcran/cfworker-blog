---
title: "leetcode-二分查找88"
date: 2026-09-28T12:29:16+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 无限字符串里第 K 个数字

给你一个整数 k 。

一个 无限 字符串是通过将所有 正 整数的 十进制 表示不添加任何分隔符 拼接 而成的字符串。

对于每个非负整数 b ，块 b 包含从 10 * b 到 10 * b + 9 的 正 整数。每个块中的整数按以下方式附加：

如果 b 是偶数，则按 递增 顺序附加整数。
如果 b 是奇数，则按 递减 顺序附加整数。

因此，字符串以整数 1 到 9 开始，接着是 19 到 10 ，然后是 20 到 29 ，接着是 39 到 30 ，依此类推。

返回该字符串的第 k 位数字（下标从 1 开始）。


```
impl Solution {
    pub fn kth_digit(mut k: i64) -> i32 {
        k -= 1; // 转为 0-based 方便计算

        // 找到第 k 位所在整数的十进制长度 length
        // 长度为 length 的正整数共有 9 * 10^(length-1) 个
        let mut cnt = 9_i64;
        let mut length = 1_i64;
        while cnt * length <= k {
            k -= cnt * length;
            cnt *= 10;
            length += 1;
        }

        // 确定第 k 位所在的整数 x（按递增顺序编号）
        let mut x = cnt / 9 + k / length;

        // 若 x 所在块的 b 为奇数，则该块按递减顺序排列，做映射调整
        // 块 b = x / 10，b 为奇数时递增序列里的 x 对应实际递减序列的 10*b + (9 - x%10)
        if (x / 10) % 2 == 1 {
            x += 9 - (x % 10) * 2;
        }

        // 取 x 从左到右第 k % length 位（0-based）
        for _ in 0..length - k % length - 1 {
            x /= 10;
        }
        (x % 10) as i32
    }
}
```
