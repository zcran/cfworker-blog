---
title: "leetcode-贪心56"
date: 2026-10-03T12:55:37+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 最多的不重叠子字符串

给你一个只包含小写字母的字符串 s ，你需要找到 s 中最多数目的非空子字符串，满足如下条件：

1. 这些字符串之间互不重叠，也就是说对于任意两个子字符串 s[i..j] 和 s[x..y] ，要么 j < x 要么 i > y 。
2. 如果一个子字符串包含字符 char ，那么 s 中所有 char 字符都应该在这个子字符串中。

请你找到满足上述条件的最多子字符串数目。如果有多个解法有相同的子字符串数目，请返回这些子字符串总长度最小的一个解。可以证明最小总长度解是唯一的。

请注意，你可以以 任意 顺序返回最优解的子字符串。


```
impl Solution {
    pub fn max_num_of_substrings(s: String) -> Vec<String> {
        let bytes = s.as_bytes();

        // 每个字符（0..26）第一次和最后一次出现的位置
        let mut first = [usize::MAX; 26];
        let mut last = [0usize; 26];
        for (i, &b) in bytes.iter().enumerate() {
            let c = (b - b'a') as usize;
            if first[c] == usize::MAX {
                first[c] = i;
            }
            last[c] = i;
        }

        // 生成所有合法区间 [start, end]
        let mut intervals: Vec<(usize, usize)> = Vec::new();
        for i in 0..bytes.len() {
            // 从每个字符的首次出现处作为区间起点尝试扩展
            if i != first[(bytes[i] - b'a') as usize] {
                continue;
            }
            let (mut l, mut r) = (i, last[(bytes[i] - b'a') as usize]);
            let mut j = i;
            let mut valid = true;
            while j <= r {
                let c = (bytes[j] - b'a') as usize;
                // 该字符的首次出现早于当前区间起点，说明起点不对
                if first[c] < l {
                    valid = false;
                    break;
                }
                // 向右扩展区间
                if last[c] > r {
                    r = last[c];
                }
                j += 1;
            }
            if valid {
                intervals.push((l, r));
            }
        }

        // 按右端点升序排序，贪心选择互不重叠的区间
        intervals.sort_unstable_by_key(|&(_, r)| r);

        let mut ans = Vec::new();
        let mut end = usize::MAX; // 上一个选中区间的右端点
        for (l, r) in intervals {
            if end == usize::MAX || l > end {
                ans.push(s[l..=r].to_string());
                end = r;
            }
        }
        ans
    }
}
```
