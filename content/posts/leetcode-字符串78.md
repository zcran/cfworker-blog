---
title: "leetcode-字符串78"
date: 2026-10-10T12:56:58+08:00
tags: ["leetcode", "字符串"]
draft: false
---


## 相邻字符串之间的最长公共前缀

给你一个字符串数组 words，对于范围 [0, words.length - 1] 内的每个下标 i，执行以下步骤：

从 words 数组中移除下标 i 处的元素。

计算修改后的数组中所有 相邻对 之间的 最长公共前缀 的长度。

返回一个数组 answer，其中 answer[i] 是移除下标 i 后，相邻对之间最长公共前缀的长度。如果 不存在 相邻对，或者 不存在 公共前缀，则 answer[i] 应为 0。

字符串的前缀是从字符串的开头开始延伸到任意位置的子字符串。


```
impl Solution {
    pub fn longest_common_prefix(words: Vec<String>) -> Vec<i32> {
        let n = words.len();

        // 计算两个字符串的最长公共前缀长度
        let lcp = |a: &str, b: &str| -> i32 {
            a.bytes()
                .zip(b.bytes())
                .take_while(|(x, y)| x == y)
                .count() as i32
        };

        // 找出相邻对 LCP 的最大值 mx1 和次大值 mx2，并记录最大值所在下标 i1
        let (mut mx1, mut mx2, mut i1) = (-1i32, -1i32, usize::MAX);
        for i in 0..n.saturating_sub(1) {
            let l = lcp(&words[i], &words[i + 1]);
            if l > mx1 {
                mx2 = mx1;
                mx1 = l;
                i1 = i;
            } else if l > mx2 {
                mx2 = l;
            }
        }

        let mut ans = vec![0i32; n];
        for i in 0..n {
            // 移除 i 后新形成的相邻对（words[i-1], words[i+1]）的 LCP
            let l = if i > 0 && i < n - 1 {
                lcp(&words[i - 1], &words[i + 1])
            } else {
                0
            };
            // 若最大 LCP 对应的相邻对未被移除，则仍可用 mx1，否则用 mx2
            ans[i] = if i != i1 && i != i1 + 1 {
                mx1.max(l)
            } else {
                mx2.max(l)
            };
        }

        ans
    }
}
```
