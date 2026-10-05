---
title: "leetcode-双指针134"
date: 2026-09-05T09:53:09+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 字符串变换后的最少分组数

给你一个字符串数组 words。

定义对字符串 s 的一次 变换 如下：

· 令 E 为 s 中位于偶数下标处字符组成的 子序列。
· 令 O 为 s 中位于奇数下标处字符组成的 子序列。
· 分别将 E 和 O 向右循环移动 任意 个位置，移动次数可以为 0。
· 将移动后的 E 中的字符依次放回偶数下标，将移动后的 O 中的字符依次放回奇数下标，从而重新构造字符串。

如果一个字符串可以通过 一次 变换得到另一个字符串，则称这两个字符串 等价 。

将 words 划分为 最少 数量的组，并满足：

· 每个字符串 恰好 属于一个组。
· 同一组中的任意两个字符串都 等价。

返回一个整数，表示所需的 最少 分组数量。

子序列 是指通过删除一个序列中的某些元素或不删除任何元素，并且不改变剩余元素相对顺序后得到的序列。


```
use std::collections::HashSet;

impl Solution {
    pub fn minimum_groups(words: Vec<String>) -> i32 {
        // 返回 s 的字典序最小的循环同构串（最小表示法，O(n)）
        fn smallest_representation(s: &[u8]) -> Vec<u8> {
            let n = s.len();
            if n == 0 {
                return Vec::new();
            }
            let mut ss = s.to_vec();
            ss.extend_from_slice(s); // ss = s + s

            let mut i = 0usize; // 当前最小子串的首下标
            let mut j = 1usize; // 待比较的子串首下标
            while j < n {
                let mut k = 0;
                while k < n && ss[i + k] == ss[j + k] {
                    k += 1;
                }
                if k >= n {
                    // s 是周期串，后续不会更小
                    break;
                }
                if ss[i + k] < ss[j + k] {
                    // i 开头更小，排除 j..=j+k
                    j += k + 1;
                } else {
                    // j 开头更小，排除 i..=i+k，更新 i=j
                    let tmp = j;
                    j = j.max(i + k) + 1;
                    i = tmp;
                }
            }
            ss[i..i + n].to_vec()
        }

        let mut set: HashSet<Vec<u8>> = HashSet::new();

        for word in &words {
            let w = word.as_bytes();
            let n = w.len();

            // 按下标奇偶性拆分成两组
            let mut groups: [Vec<u8>; 2] = [Vec::new(), Vec::new()];
            for (idx, &c) in w.iter().enumerate() {
                groups[idx % 2].push(c);
            }

            // 分别求偶数组、奇数组的最小表示，再按原奇偶位置拼回
            let mut min_s = vec![0u8; n];
            for k in 0..2 {
                let ms = smallest_representation(&groups[k]);
                for (j, &c) in ms.iter().enumerate() {
                    min_s[j * 2 + k] = c;
                }
            }

            set.insert(min_s);
        }

        set.len() as i32
    }
}
```
