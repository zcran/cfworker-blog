---
title: "leetcode-栈38"
date: 2026-07-24T10:16:59+08:00
tags: ["leetcode", "栈"]
draft: false
---


## 使字符串平衡的最少删除次数

给你一个字符串 s ，它仅包含字符 'a' 和 'b'​​​​ 。

你可以删除 s 中任意数目的字符，使得 s 平衡 。当不存在下标对 (i,j) 满足 i < j ，且 s[i] = 'b' 的同时 s[j]= 'a' ，此时认为 s 是 平衡 的。

请你返回使 s 平衡 的 最少 删除次数。


```
impl Solution {
    pub fn minimum_deletions(s: String) -> i32 {
        // 最终平衡字符串的形式为：a...ab...b（所有 a 在 b 前面）
        // 可以枚举分界点：左边保留 a，右边保留 b
        let total_a = s.chars().filter(|&c| c == 'a').count() as i32;
        let mut left_b = 0;      // 当前分界点左侧的 b 数量（需要删除）
        let mut right_a = total_a; // 当前分界点右侧的 a 数量（需要删除）
        let mut min_deletions = total_a; // 初始：删除所有 a

        for ch in s.chars() {
            if ch == 'a' {
                right_a -= 1;    // 当前 a 移到左侧，右侧 a 减少
            } else {
                left_b += 1;     // 当前 b 留在右侧，左侧 b 增加
            }
            // 当前分界点需要的删除次数 = 左侧 b + 右侧 a
            min_deletions = min_deletions.min(left_b + right_a);
        }

        min_deletions
    }
}
```
