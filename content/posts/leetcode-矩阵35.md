---
title: "leetcode-矩阵35"
date: 2026-09-19T10:41:34+08:00
tags: ["leetcode", "矩阵"]
draft: false
---


## 最富有客户的资产总量

给你一个 m x n 的整数网格 accounts ，其中 accounts[i][j] 是第 i 位客户在第 j 家银行托管的资产数量。返回最富有客户所拥有的 资产总量 。

客户的 资产总量 就是他们在各家银行托管的资产数量之和。最富有客户就是 资产总量 最大的客户。


```
impl Solution {
    pub fn maximum_wealth(accounts: Vec<Vec<i32>>) -> i32 {
        // 每行求和即为该客户的总资产，取最大值即为最富有客户的资产总量
        accounts
            .iter()
            .map(|row| row.iter().sum())
            .max()
            .unwrap()
    }
}
```
