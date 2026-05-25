---
title: "leetcode-记忆化26"
date: 2026-05-06T20:22:34+08:00
tags: ["leetcode", "记忆化"]
draft: false
---


## 小于等于 K 的最长二进制子序列

给你一个二进制字符串 s 和一个正整数 k 。

请你返回 s 的 最长 子序列的长度，且该子序列对应的 二进制 数字小于等于 k 。

注意：

子序列可以有 前导 0 。
空字符串视为 0 。
子序列 是指从一个字符串中删除零个或者多个字符后，不改变顺序得到的剩余字符序列。

```
impl Solution {
    /// 在二进制字符串 `s` 中找出最长的子序列，使其表示的二进制数值不超过 `k`。
    /// 子序列不需连续，但需保持原字符串中的相对顺序。
    ///
    /// 算法思路：
    /// 1. 计算 `k` 的二进制长度 `len_k`。若 `s` 长度小于 `len_k`，则整个 `s` 都可选（因为任何数都不会超过 `k`）。
    /// 2. 考虑选择长度为 `len_k` 的后缀作为数值部分，因为后缀在序列中位置靠后，更容易在它前面添加更多的 `0`。
    ///    - 检查该后缀的数值是否 ≤ `k`。若是，则这个后缀可以全取；否则，最多只能取 `len_k - 1` 位。
    /// 3. 对于后缀之前的字符，任何 `0` 都可以加入子序列（不会增大数值），而 `1` 不能加（会使数值变大）。
    /// 4. 最终子序列长度 = (可用的后缀长度) + (后缀之前所有 `0` 的数量)。
    ///
    /// # 参数
    /// - `s`: 二进制字符串，只包含字符 '0' 和 '1'
    /// - `k`: 整数上限 (0 ≤ k ≤ 10^9)
    ///
    /// # 返回值
    /// 最长满足条件的子序列长度
    pub fn longest_subsequence(s: String, k: i32) -> i32 {
        let bin_str = s.as_bytes();          // 字节数组便于索引
        let n = bin_str.len();               // 字符串总长度
        let bits_of_k = (32 - k.leading_zeros()) as usize; // k 的二进制位数（不含前导零）

        // 情况1：整个字符串的长度小于 k 的位数 → 全选
        if n < bits_of_k {
            return n as i32;
        }

        // 取最后 bits_of_k 位作为后缀（这些位在子序列中尽量靠后）
        let suffix_start = n - bits_of_k;
        let suffix_str = std::str::from_utf8(&bin_str[suffix_start..]).unwrap();
        let suffix_val = i32::from_str_radix(suffix_str, 2).unwrap();

        // 后缀部分最多能取的长度（要么全取 bits_of_k，要么少一位）
        let suffix_len = if suffix_val <= k { bits_of_k } else { bits_of_k - 1 };

        // 后缀之前的部分：所有 '0' 都可以取，'1' 不能取
        let leading_zeros = bin_str[..suffix_start]
            .iter()
            .filter(|&&c| c == b'0')
            .count();

        (suffix_len + leading_zeros) as i32
    }
}
```
