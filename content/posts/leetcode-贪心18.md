---
title: "leetcode-贪心18"
date: 2026-10-03T12:55:35+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 最长数对链

给你一个由 n 个数对组成的数对数组 pairs ，其中 pairs[i] = [lefti, righti] 且 lefti < righti 。

现在，我们定义一种 跟随 关系，当且仅当 b < c 时，数对 p2 = [c, d] 才可以跟在 p1 = [a, b] 后面。我们用这种形式来构造 数对链 。

找出并返回能够形成的 最长数对链的长度 。

你不需要用到所有的数对，你可以以任何顺序选择其中的一些数对来构造。


```
impl Solution {
    pub fn find_longest_chain(mut pairs: Vec<Vec<i32>>) -> i32 {
        // 贪心：按右端点升序排序，优先选择右端点小、更"早结束"的数对，
        // 从而给后续数对留下更多空间。注意条件为严格大于：p[0] > pre_r。
        pairs.sort_unstable_by_key(|p| p[1]);

        let (mut ans, mut pre_r) = (0, i32::MIN);
        for p in &pairs {
            if p[0] > pre_r {
                ans += 1;
                pre_r = p[1];
            }
        }

        ans
    }
}
```
