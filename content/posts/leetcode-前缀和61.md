---
title: "leetcode-前缀和61"
date: 2026-08-25T10:02:49+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 选择建筑的方案数

给你一个下标从 0 开始的二进制字符串 s ，它表示一条街沿途的建筑类型，其中：

s[i] = '0' 表示第 i 栋建筑是一栋办公楼，
s[i] = '1' 表示第 i 栋建筑是一间餐厅。

作为市政厅的官员，你需要随机 选择 3 栋建筑。然而，为了确保多样性，选出来的 3 栋建筑 相邻 的两栋不能是同一类型。

比方说，给你 s = "001101" ，我们不能选择第 1 ，3 和 5 栋建筑，因为得到的子序列是 "011" ，有相邻两栋建筑是同一类型，所以 不合 题意。

请你返回可以选择 3 栋建筑的 有效方案数 。




```
impl Solution {
    pub fn number_of_ways(s: String) -> i64 {
        let bytes = s.as_bytes();
        let n = bytes.len();

        // 统计餐厅（'1'）的总数
        let total_ones = bytes.iter().filter(|&&b| b == b'1').count();

        let mut ones_seen = 0;    // 当前位置之前遇到的 '1' 的数量
        let mut zeros_seen = 0;   // 当前位置之前遇到的 '0' 的数量
        let mut ans = 0;

        for &b in bytes {
            if b == b'1' {
                // 以当前 '1' 作为中间元素：左边选 '0'，右边选 '0'
                // 左边可以有 zeros_seen 个选择，右边可以有 (总0数 - zeros_seen) 个选择
                let total_zeros = n - total_ones;
                ans += zeros_seen as i64 * (total_zeros - zeros_seen) as i64;
                ones_seen += 1;
            } else {
                // 以当前 '0' 作为中间元素：左边选 '1'，右边选 '1'
                // 左边可以有 ones_seen 个选择，右边可以有 (总1数 - ones_seen) 个选择
                ans += ones_seen as i64 * (total_ones - ones_seen) as i64;
                zeros_seen += 1;
            }
        }

        ans
    }
}
```
