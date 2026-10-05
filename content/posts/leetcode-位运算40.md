---
title: "leetcode-位运算40"
date: 2026-09-24T10:33:36+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 找出前缀异或的原始数组

给你一个长度为 n 的 整数 数组 pref 。找出并返回满足下述条件且长度为 n 的数组 arr ：

pref[i] = arr[0] ^ arr[1] ^ ... ^ arr[i].

注意 ^ 表示 按位异或（bitwise-xor）运算。

可以证明答案是 唯一 的。


```
impl Solution {
    /// 由前缀异或数组 pref 还原原数组 arr。
    ///
    /// 由 `pref[i] = arr[0] ^ ... ^ arr[i]` 可得
    /// `arr[i] = pref[i] ^ pref[i-1]`，其中 `pref[-1] = 0`。
    /// 因此 arr 就是 pref 的相邻异或差分。
    pub fn find_array(pref: Vec<i32>) -> Vec<i32> {
        let mut prev = 0;
        pref.into_iter()
            .map(|x| {
                let cur = prev ^ x;
                prev = x;
                cur
            })
            .collect()
    }
}
```
