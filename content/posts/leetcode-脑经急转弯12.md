---
title: "leetcode-脑经急转弯12"
date: 2026-03-24T20:55:57+08:00
tags: ["leetcode", "脑筋急转弯"]
draft: false
---


## 构造最长的新字符串


给你三个整数 x ，y 和 z 。

这三个整数表示你有 x 个 "AA" 字符串，y 个 "BB" 字符串，和 z 个 "AB" 字符串。你需要选择这些字符串中的部分字符串（可以全部选择也可以一个都不选择），将它们按顺序连接得到一个新的字符串。新字符串不能包含子字符串 "AAA" 或者 "BBB" 。

请你返回 新字符串的最大可能长度。

子字符串 是一个字符串中一段连续 非空 的字符序列。


```

impl Solution {
    // 由于 AB 自己可以和自己无限拼接，然后AB 既可以拼AA，也可以拼BB
    // 所以 先考虑 AA，BB 最多可以拼多少
    //  如果 AA 和 BB 个数一样，那么AA，BB 都可以拼完: 2(x+y)
    //  如果 AA 和 BB 个数不一样，那么 可以拼 AABB...BBAA 或 BBAA...AABB, AA或BB只能多一个: 2 * x.min(y) + 1
    // 然后 AB 有多少拼多少
    pub fn longest_string(x: i32, y: i32, z: i32) -> i32 {
        if x == y {
            return (x + y + z) << 1;
        }

        return ((x.min(y) << 1) + 1 + z) << 1;
    }


}

```
