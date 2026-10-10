---
title: "leetcode-排序14"
date: 2026-10-05T00:35:00+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 自定义字符串排序

给定两个字符串 order 和 s 。order 的所有字母都是 唯一 的，并且预先按照一些自定义的顺序排序。

对 s 的字符进行置换，使其与排序的 order 相匹配。更具体地说，如果在 order 中的字符 x 出现字符 y 之前，那么在排列后的字符串中， x 也应该出现在 y 之前。

返回 满足这个性质的 s 的任意一种排列 。




```
impl Solution {
    /// 按自定义顺序 order 重排字符串 s
    ///
    /// 思路：先统计 s 中各字符出现次数，再按 order 顺序依次输出；
    /// order 中未出现的字符（题目保证 s 只含小写字母）按字典序补在末尾。
    /// 时间 O(|order| + |s| + 26)，额外空间 O(1)（计数数组固定 26）。
    pub fn custom_sort_string(order: String, s: String) -> String {
        // 统计 s 中每个小写字母的出现次数
        let mut cnt = [0usize; 26];
        for b in s.bytes() {
            cnt[(b - b'a') as usize] += 1;
        }

        // 预分配结果容量，避免多次扩容
        let mut ans = String::with_capacity(s.len());

        // 按 order 指定的顺序输出字符
        for b in order.bytes() {
            let i = (b - b'a') as usize;
            ans.extend(std::iter::repeat(b as char).take(cnt[i]));
            cnt[i] = 0;
        }

        // 剩余未在 order 中出现的字符，按字典序补齐
        for (i, &c) in cnt.iter().enumerate() {
            ans.extend(std::iter::repeat((b'a' + i as u8) as char).take(c));
        }

        ans
    }
}
```
