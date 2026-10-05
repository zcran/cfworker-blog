---
title: "leetcode-二分查找11"
date: 2026-09-28T12:29:10+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 俄罗斯套娃信封问题

给你一个二维整数数组 envelopes ，其中 envelopes[i] = [wi, hi] ，表示第 i 个信封的宽度和高度。

当另一个信封的宽度和高度都比这个信封大的时候，这个信封就可以放进另一个信封里，如同俄罗斯套娃一样。

请计算 最多能有多少个 信封能组成一组“俄罗斯套娃”信封（即可以把一个信封放到另一个信封里面）。

注意：不允许旋转信封。


```
impl Solution {
    pub fn max_envelopes(mut envelopes: Vec<Vec<i32>>) -> i32 {
        // 宽度升序；宽度相同时高度降序，避免同宽度信封被重复计入 LIS
        envelopes.sort_unstable_by_key(|e| (e[0], -e[1]));

        // 对高度序列求最长严格递增子序列（耐心排序 + 二分）
        let mut tails: Vec<i32> = Vec::with_capacity(envelopes.len());
        for e in &envelopes {
            let h = e[1];
            // 找到第一个 >= h 的位置
            let pos = tails.partition_point(|&x| x < h);
            if pos == tails.len() {
                tails.push(h);
            } else {
                tails[pos] = h;
            }
        }
        tails.len() as i32
    }
}
```
