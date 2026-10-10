---
title: "leetcode-排序62"
date: 2026-10-05T00:35:03+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 判断通过操作能否让字符串相等 II

给你两个字符串 s1 和 s2 ，两个字符串长度都为 n ，且只包含 小写 英文字母。

你可以对两个字符串中的 任意一个 执行以下操作 任意 次：

选择两个下标 i 和 j ，满足 i < j 且 j - i 是 偶数，然后 交换 这个字符串中两个下标对应的字符。


如果你可以让字符串 s1 和 s2 相等，那么返回 true ，否则返回 false 。




```
impl Solution {
    pub fn check_strings(s1: String, s2: String) -> bool {
        if s1.len() != s2.len() {
            return false;
        }

        // 交换要求 j - i 为偶数，因此奇数位之间、偶数位之间可以任意互换，
        // 但奇数位与偶数位之间无法交换。所以只需分别比较 s1 与 s2
        // 在奇数位、偶数位上各字符的数量是否一致。
        //
        // counts 分两段使用：
        //   [0, 128)    -> 偶数位字符计数（s1 加，s2 减）
        //   [128, 256)  -> 奇数位字符计数（s1 加，s2 减）
        let mut counts = [0i32; 256];

        for (i, (c1, c2)) in s1.bytes().zip(s2.bytes()).enumerate() {
            // i & 1 为 0 表示偶数位，为 1 表示奇数位；左移 7 位即乘以 128
            let offset = (i & 1) << 7;
            counts[offset + c1 as usize] += 1;
            counts[offset + c2 as usize] -= 1;
        }

        // 所有计数都抵消为 0，说明两个字符串在奇偶位上的字符分布完全一致
        counts.iter().all(|&c| c == 0)
    }
}
```
