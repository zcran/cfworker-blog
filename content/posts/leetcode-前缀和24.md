---
title: "leetcode-前缀和24"
date: 2026-08-25T10:02:47+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 形成两个异或相等数组的三元组数目

给你一个整数数组 arr 。

现需要从数组中取三个下标 i、j 和 k ，其中 (0 <= i < j <= k < arr.length) 。

a 和 b 定义如下：

a = arr[i] ^ arr[i + 1] ^ ... ^ arr[j - 1]
b = arr[j] ^ arr[j + 1] ^ ... ^ arr[k]

注意：^ 表示 按位异或 操作。

请返回能够令 a == b 成立的三元组 (i, j , k) 的数目。


```
impl Solution {
    pub fn count_triplets(arr: Vec<i32>) -> i32 {
        let n = arr.len();

        // 前缀异或：prefix[i] = arr[0] ^ ... ^ arr[i-1]
        let mut prefix = vec![0; n + 1];
        for i in 0..n {
            prefix[i + 1] = prefix[i] ^ arr[i];
        }

        let mut count = 0;

        // 枚举 i 和 k，当 prefix[i] == prefix[k+1] 时，
        // 任何 j ∈ (i, k] 都满足条件，共有 k - i 种选择
        for i in 0..n {
            for k in i + 1..n {
                if prefix[i] == prefix[k + 1] {
                    count += (k - i) as i32;
                }
            }
        }

        count
    }
}
```
