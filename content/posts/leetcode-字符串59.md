---
title: "leetcode-字符串59"
date: 2026-10-10T12:56:56+08:00
tags: ["leetcode", "字符串"]
draft: false
---


## 到目标字符串的最短距离

给你一个下标从 0 开始的 环形 字符串数组 words 和一个字符串 target 。环形数组 意味着数组首尾相连。

形式上， words[i] 的下一个元素是 words[(i + 1) % n] ，而 words[i] 的前一个元素是 words[(i - 1 + n) % n] ，其中 n 是 words 的长度。

从 startIndex 开始，你一次可以用 1 步移动到下一个或者前一个单词。

返回到达目标字符串 target 所需的最短距离。如果 words 中不存在字符串 target ，返回 -1 。




```
impl Solution {
    pub fn closest_target(words: Vec<String>, target: String, start_index: i32) -> i32 {
        let n = words.len() as i32;
        let mut ans = n;

        for (i, word) in words.iter().enumerate() {
            if word == &target {
                // 环形数组中，两方向距离取较小值
                let dist = (i as i32 - start_index).abs();
                ans = ans.min(dist.min(n - dist));
            }
        }

        if ans == n { -1 } else { ans }
    }
}
```
