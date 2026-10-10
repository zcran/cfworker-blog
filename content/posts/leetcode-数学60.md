---
title: "leetcode-数学60"
date: 2026-10-07T20:39:32+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 交替子数组计数

给你一个二进制数组nums 。

如果一个子数组中 不存在 两个 相邻 元素的值 相同 的情况，我们称这样的子数组为 交替子数组 。

返回数组 nums 中交替子数组的数量。


```
impl Solution {
    pub fn count_alternating_subarrays(nums: Vec<i32>) -> i64 {
        // cur：以当前元素结尾的最长交替子数组长度
        // 若当前元素与前一元素不同，则 cur += 1；否则交替中断，cur = 1
        // 以当前元素结尾的交替子数组共有 cur 个，累加到结果中
        let mut res = 0i64;
        let mut cur = 0i64;
        let mut prev = -1;

        for &x in &nums {
            cur = if x != prev { cur + 1 } else { 1 };
            prev = x;
            res += cur;
        }

        res
    }
}
```
