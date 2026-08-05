---
title: "leetcode-枚举46"
date: 2026-07-09T10:05:01+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 生成特殊数字的最少操作

给你一个下标从 0 开始的字符串 num ，表示一个非负整数。

在一次操作中，您可以选择 num 的任意一位数字并将其删除。请注意，如果你删除 num 中的所有数字，则 num 变为 0。

返回最少需要多少次操作可以使 num 变成特殊数字。

如果整数 x 能被 25 整除，则该整数 x 被认为是特殊数字。


```
impl Solution {
    pub fn minimum_operations(num: String) -> i32 {
        let bytes = num.as_bytes();
        let n = bytes.len();

        // 记录从右向左扫描时，是否已经遇到过 '0' 或 '5'
        let mut seen_0 = false;
        let mut seen_5 = false;

        // 从右向左遍历，寻找能构成 25 的倍数的末尾两位组合
        for i in (0..n).rev() {
            match bytes[i] {
                b'0' => {
                    // 如果已经遇到过 '0'，则末尾两位是 "00"（或 "50" 但由后续逻辑处理）
                    if seen_0 {
                        // 保留当前 '0' 和之前遇到的 '0'，删除中间所有数字
                        return (n - i - 2) as i32;
                    }
                    seen_0 = true;
                }
                b'5' => {
                    // 如果已经遇到过 '0'，则末尾两位是 "50"（25 的倍数）
                    if seen_0 {
                        return (n - i - 2) as i32;
                    }
                    // 如果已经遇到过 '5'，则末尾两位是 "55"（不是 25 的倍数）
                    if seen_5 {
                        // 不构成 25 的倍数，继续向前找
                    } else {
                        seen_5 = true;
                    }
                }
                b'2' | b'7' => {
                    // 如果已经遇到过 '5'，则末尾两位是 "25" 或 "75"（25 的倍数）
                    if seen_5 {
                        return (n - i - 2) as i32;
                    }
                }
                _ => {} // 其他数字忽略
            }
        }

        // 没有找到成对的末尾组合，检查能否只保留一个 '0'
        if seen_0 {
            // 删除除最后一个 '0' 外的所有数字，结果为 0（0 是 25 的倍数）
            return (n - 1) as i32;
        }

        // 否则删除所有数字，结果为 0
        n as i32
    }
}
```
