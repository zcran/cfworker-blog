---
title: "leetcode-双指针82"
date: 2026-09-05T09:53:06+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 可移除字符的最大数目

给你两个字符串 s 和 p ，其中 p 是 s 的一个 子序列 。同时，给你一个元素 互不相同 且下标 从 0 开始 计数的整数数组 removable ，该数组是 s 中下标的一个子集（s 的下标也 从 0 开始 计数）。

请你找出一个整数 k（0 <= k <= removable.length），选出 removable 中的 前 k 个下标，然后从 s 中移除这些下标对应的 k 个字符。整数 k 需满足：在执行完上述步骤后， p 仍然是 s 的一个 子序列 。更正式的解释是，对于每个 0 <= i < k ，先标记出位于 s[removable[i]] 的字符，接着移除所有标记过的字符，然后检查 p 是否仍然是 s 的一个子序列。

返回你可以找出的 最大 k ，满足在移除字符后 p 仍然是 s 的一个子序列。

字符串的一个 子序列 是一个由原字符串生成的新字符串，生成过程中可能会移除原字符串中的一些字符（也可能不移除）但不改变剩余字符之间的相对顺序。


```
impl Solution {
    pub fn maximum_removals(s: String, p: String, removable: Vec<i32>) -> i32 {
        let (s, p) = (s.as_bytes(), p.as_bytes());
        let mut lo = 0;
        let mut hi = removable.len();

        // 二分查找最大的可行 k
        while lo < hi {
            let mid = lo + (hi - lo + 1) / 2;

            // 标记前 mid 个待移除的下标
            let mut removed = vec![false; s.len()];
            for &idx in &removable[..mid] {
                removed[idx as usize] = true;
            }

            // 若移除后 p 仍是 s 的子序列，则尝试更大的 k
            if Self::is_subsequence(s, p, &removed) {
                lo = mid;
            } else {
                hi = mid - 1;
            }
        }

        lo as i32
    }

    /// 判断 p 是否为 s（忽略被标记位置后）的子序列
    fn is_subsequence(s: &[u8], p: &[u8], removed: &[bool]) -> bool {
        let mut i = 0;
        for (j, &c) in s.iter().enumerate() {
            if !removed[j] && i < p.len() && c == p[i] {
                i += 1;
                if i == p.len() {
                    return true;
                }
            }
        }
        i == p.len()
    }
}
```
