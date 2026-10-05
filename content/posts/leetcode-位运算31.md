---
title: "leetcode-位运算31"
date: 2026-09-24T10:33:36+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 解码异或后的数组

未知 整数数组 arr 由 n 个非负整数组成。

经编码后变为长度为 n - 1 的另一个整数数组 encoded ，其中 encoded[i] = arr[i] XOR arr[i + 1] 。例如，arr = [1,0,2,1] 经编码后得到 encoded = [1,2,3] 。

给你编码后的数组 encoded 和原数组 arr 的第一个元素 first（arr[0]）。

请解码返回原数组 arr 。可以证明答案存在并且是唯一的。


```
impl Solution {
    /// 根据编码数组 encoded 和首元素 first 还原原数组 arr。
    ///
    /// 由 `encoded[i] = arr[i] ^ arr[i+1]` 可得 `arr[i+1] = encoded[i] ^ arr[i]`，
    /// 从 first 出发依次推导即可。
    pub fn decode(encoded: Vec<i32>, first: i32) -> Vec<i32> {
        let mut arr = Vec::with_capacity(encoded.len() + 1);
        arr.push(first);
        for &e in &encoded {
            let next = arr.last().unwrap() ^ e;
            arr.push(next);
        }
        arr
    }
}
```
