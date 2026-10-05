---
title: "leetcode-位运算32"
date: 2026-09-24T10:33:36+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 解码异或后的排列

给你一个整数数组 perm ，它是前 n 个正整数的排列，且 n 是个 奇数 。

它被加密成另一个长度为 n - 1 的整数数组 encoded ，满足 encoded[i] = perm[i] XOR perm[i + 1] 。比方说，如果 perm = [1,3,2] ，那么 encoded = [2,1] 。

给你 encoded 数组，请你返回原始数组 perm 。题目保证答案存在且唯一。


```
impl Solution {
    /// 根据编码数组 encoded 还原原始排列 perm。
    ///
    /// perm 是 1..=n 的排列（n 为奇数），且 `encoded[i] = perm[i] ^ perm[i+1]`。
    ///
    /// 关键：先求出 perm[0]。
    /// 记 total = 1 ^ 2 ^ ... ^ n，
    /// 则 perm[0] = total ^ perm[1] ^ perm[2] ^ ... ^ perm[n-1]。
    /// 而 perm[1] ^ perm[2] ^ ... ^ perm[n-1]
    ///          = (perm[1]^perm[2]) ^ (perm[3]^perm[4]) ^ ...
    ///          = encoded[1] ^ encoded[3] ^ ...
    /// 因为 n 为奇数，n-1 为偶数，正好取 encoded 的奇数下标项。
    /// 得到 perm[0] 后，依次 `perm[i+1] = perm[i] ^ encoded[i]` 即可还原。
    pub fn decode(encoded: Vec<i32>) -> Vec<i32> {
        let n = encoded.len() + 1;

        // 1 ^ 2 ^ ... ^ n
        let total = (1..=n as i32).fold(0, |acc, x| acc ^ x);

        // encoded 奇数下标的异或和，即 perm[1] ^ perm[2] ^ ... ^ perm[n-1]
        let tail_xor = encoded.iter().skip(1).step_by(2).fold(0, |acc, &x| acc ^ x);

        // 反推出首元素
        let mut prev = total ^ tail_xor;

        // 依次还原整个排列
        let mut perm = Vec::with_capacity(n);
        perm.push(prev);
        for &e in &encoded {
            prev ^= e;
            perm.push(prev);
        }

        perm
    }
}
```
