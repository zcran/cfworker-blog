---
title: "leetcode-模拟53"
date: 2026-08-08T11:31:11+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 将一维数组转变成二维数组

给你一个下标从 0 开始的一维整数数组 original 和两个整数 m 和  n 。你需要使用 original 中 所有 元素创建一个 m 行 n 列的二维数组。

original 中下标从 0 到 n - 1 （都 包含 ）的元素构成二维数组的第一行，下标从 n 到 2 * n - 1 （都 包含 ）的元素构成二维数组的第二行，依此类推。

请你根据上述过程返回一个 m x n 的二维数组。如果无法构成这样的二维数组，请你返回一个空的二维数组。


```
impl Solution {
    pub fn construct2_d_array(original: Vec<i32>, m: i32, n: i32) -> Vec<Vec<i32>> {
        let m = m as usize;
        let n = n as usize;

        // 元素总数必须严格等于 m * n，否则无法构成目标矩阵
        if original.len() != m * n {
            return vec![];
        }

        // chunks(n) 将原数组按每 n 个元素切分为一行
        // 预先分配外层 Vec 的容量，避免动态扩容
        let mut res = Vec::with_capacity(m);
        for row in original.chunks(n) {
            res.push(row.to_vec());
        }
        res
    }
}
```
