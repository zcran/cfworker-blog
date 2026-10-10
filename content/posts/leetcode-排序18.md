---
title: "leetcode-排序18"
date: 2026-10-05T00:35:00+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 特殊等价字符串组

给你一个字符串数组 words。

一步操作中，你可以交换字符串 words[i] 的任意两个偶数下标对应的字符或任意两个奇数下标对应的字符。

对两个字符串 words[i] 和 words[j] 而言，如果经过任意次数的操作，words[i] == words[j] ，那么这两个字符串是 特殊等价 的。

例如，words[i] = "zzxy" 和 words[j] = "xyzz" 是一对 特殊等价 字符串，因为可以按 "zzxy" -> "xzzy" -> "xyzz" 的操作路径使 words[i] == words[j] 。

现在规定，words 的 一组特殊等价字符串 就是 words 的一个同时满足下述条件的非空子集：

该组中的每一对字符串都是 特殊等价 的

该组字符串已经涵盖了该类别中的所有特殊等价字符串，容量达到理论上的最大值（也就是说，如果一个字符串不在该组中，那么这个字符串就 不会 与该组内任何字符串特殊等价）

返回 words 中 特殊等价字符串组 的数量。


```
use std::collections::HashSet;

impl Solution {
    /// 统计 words 中「特殊等价字符串组」的数量
    ///
    /// 思路：一次操作只能交换同奇偶下标上的字符，因此两个字符串特殊等价
    /// 当且仅当它们的偶数位字符集合（含重复次数）与奇数位字符集合分别相同。
    /// 于是可把每个字符串归一化为一个「签名」：前 26 位统计偶数位字符频次，
    /// 后 26 位统计奇数位字符频次。不同签名的数量即为组数。
    pub fn num_special_equiv_groups(words: Vec<String>) -> i32 {
        let mut groups = HashSet::new();

        for word in words {
            // 签名：前 26 位为偶数下标字符频次，后 26 位为奇数下标字符频次
            let mut sig = [0u8; 52];
            for (i, b) in word.bytes().enumerate() {
                // i & 1 == 0 -> 偶数位，偏移 0；否则奇数位，偏移 26
                sig[(b - b'a') as usize + (i & 1) * 26] += 1;
            }
            groups.insert(sig);
        }

        groups.len() as i32
    }
}
```
