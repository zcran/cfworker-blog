---
title: "leetcode-哈希表56"
date: 2026-10-09T20:07:59+08:00
tags: ["leetcode", "哈希表"]
draft: false
---


## 检查相同字母间的距离

给你一个下标从 0 开始的字符串 s ，该字符串仅由小写英文字母组成，s 中的每个字母都 恰好 出现 两次 。另给你一个下标从 0 开始、长度为 26 的的整数数组 distance 。

字母表中的每个字母按从 0 到 25 依次编号（即，'a' -> 0, 'b' -> 1, 'c' -> 2, ... , 'z' -> 25）。

在一个 匀整 字符串中，第 i 个字母的两次出现之间的字母数量是 distance[i] 。如果第 i 个字母没有在 s 中出现，那么 distance[i] 可以 忽略 。

如果 s 是一个 匀整 字符串，返回 true ；否则，返回 false 。


```
impl Solution {
    pub fn check_distances(s: String, distance: Vec<i32>) -> bool {
        // first[i] 记录字母 ('a' + i) 首次出现的下标，-1 表示尚未出现
        let mut first = [-1i32; 26];

        for (i, b) in s.bytes().enumerate() {
            let idx = (b - b'a') as usize;
            let i = i as i32;

            // 第二次出现：校验两次出现之间的字母数量
            if first[idx] != -1 && i - first[idx] - 1 != distance[idx] {
                return false;
            }
            first[idx] = i;
        }

        true
    }
}
```
