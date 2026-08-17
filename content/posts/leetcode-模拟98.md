---
title: "leetcode-模拟98"
date: 2026-08-08T11:31:14+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 上一个遍历的整数

给你一个整数数组 nums ，其中 nums[i] 要么是一个正整数，要么是 -1 。我们需要为每个 -1 找到相应的正整数，我们称之为最后访问的整数。

为了达到这个目标，定义两个空数组：seen 和 ans。

从数组 nums 的头部开始遍历。

· 如果遇到正整数，把它添加到 seen 的 头部。
· 如果遇到 -1，则设 k 是到目前为止看到的 连续 -1 的数目(包括当前 -1)，
    · 如果 k 小于等于 seen 的长度，把 seen 的第 k 个元素添加到 ans。
    · 如果 k 严格大于 seen 的长度，把 -1 添加到 ans。

请你返回数组 ans。


```
impl Solution {
    pub fn last_visited_integers(nums: Vec<i32>) -> Vec<i32> {
        let mut seen = Vec::new();
        let mut ans = Vec::new();
        let mut k = 0;

        for &num in &nums {
            if num == -1 {
                k += 1;
                // 从尾部取第 k 个元素，越界则返回 -1
                ans.push(if k <= seen.len() { seen[seen.len() - k] } else { -1 });
            } else {
                seen.push(num);   // 正整数追加到尾部
                k = 0;            // 重置连续 -1 计数
            }
        }

        ans
    }
}
```
