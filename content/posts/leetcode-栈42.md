---
title: "leetcode-栈42"
date: 2026-07-24T10:16:59+08:00
tags: ["leetcode", "栈"]
draft: false
---


##  使字符串平衡的最小交换次数

给你一个字符串 s ，下标从 0 开始 ，且长度为偶数 n 。字符串 恰好 由 n / 2 个开括号 '[' 和 n / 2 个闭括号 ']' 组成。

只有能满足下述所有条件的字符串才能称为 平衡字符串 ：

字符串是一个空字符串，或者
字符串可以记作 AB ，其中 A 和 B 都是 平衡字符串 ，或者
字符串可以写成 [C] ，其中 C 是一个 平衡字符串 。

你可以交换 任意 两个下标所对应的括号 任意 次数。

返回使 s 变成 平衡字符串 所需要的 最小 交换次数。


```
impl Solution {
    pub fn min_swaps(s: String) -> i32 {
        let mut balance = 0;      // 当前平衡度：'[' 为 +1，']' 为 -1
        let mut min_balance = 0;  // 遍历过程中的最小平衡度

        for ch in s.chars() {
            if ch == '[' {
                balance += 1;
            } else {
                balance -= 1;
                min_balance = min_balance.min(balance);
            }
        }

        // 最小平衡度的绝对值表示需要匹配的未闭合 ']' 数量
        // 每次交换可以修复最多 2 个不平衡的 ']'
        (-min_balance + 1) / 2
    }
}
```
