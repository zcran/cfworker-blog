---
title: "leetcode-二分查找60"
date: 2026-09-28T12:29:14+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 数组中最长的方波

给你一个整数数组 nums 。如果 nums 的子序列满足下述条件，则认为该子序列是一个 方波 ：

子序列的长度至少为 2 ，并且

将子序列从小到大排序 之后 ，除第一个元素外，每个元素都是前一个元素的 平方 。

返回 nums 中 最长方波 的长度，如果不存在 方波 则返回 -1 。

子序列 也是一个数组，可以由另一个数组删除一些或不删除元素且不改变剩余元素的顺序得到。


```
impl Solution {
    pub fn longest_square_streak(mut nums: Vec<i32>) -> i32 {
        // 按升序排序，保证处理 n 时其平方根已经处理过
        nums.sort_unstable();

        // dp[x]：以值 x 结尾的最长方波长度
        let mut dp: std::collections::HashMap<i32, i32> = std::collections::HashMap::new();
        let mut ans = 0;

        for &n in &nums {
            // 若 n 是完全平方数且其平方根存在，则可接在平方根后面
            let r = (n as f64).sqrt() as i32;
            let len = if r * r == n {
                dp.get(&r).map_or(1, |&v| v + 1)
            } else {
                1
            };
            dp.insert(n, len);
            ans = ans.max(len);
        }

        // 长度至少为 2 才算方波
        if ans >= 2 { ans } else { -1 }
    }
}
```
