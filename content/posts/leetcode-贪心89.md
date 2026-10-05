---
title: "leetcode-贪心89"
date: 2026-10-03T12:55:40+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 喂食仓鼠的最小食物桶数

给你一个下标从 0 开始的字符串 hamsters ，其中 hamsters[i]  要么是：

'H' 表示有一个仓鼠在下标 i ，或者
'.' 表示下标 i 是空的。

你将要在空的位置上添加一定数量的食物桶来喂养仓鼠。如果仓鼠的左边或右边至少有一个食物桶，就可以喂食它。更正式地说，如果你在位置 i - 1 或者 i + 1 放置一个食物桶，就可以喂养位置为 i 处的仓鼠。

在 空的位置 放置食物桶以喂养所有仓鼠的前提下，请你返回需要的 最少 食物桶数。如果无解请返回 -1 。




```
impl Solution {
    pub fn minimum_buckets(street: String) -> i32 {
        let bytes = street.as_bytes();
        let n = bytes.len();
        let mut buckets = 0;
        let mut i = 0;

        // 贪心：遇到仓鼠时，优先在它右侧放桶（能顺带喂下一只），否则放左侧
        while i < n {
            if bytes[i] == b'H' {
                if i > 0 && bytes[i - 1] == b'B' {
                    // 左侧已有桶，当前仓鼠已被喂
                } else if i + 1 < n && bytes[i + 1] == b'.' {
                    // 在右侧放桶，标记为已放置
                    unsafe { (bytes.as_ptr() as *mut u8).add(i + 1).write(b'B'); }
                    buckets += 1;
                } else if i > 0 && bytes[i - 1] == b'.' {
                    // 右侧没有空位，尝试在左侧放桶
                    unsafe { (bytes.as_ptr() as *mut u8).add(i - 1).write(b'B'); }
                    buckets += 1;
                } else {
                    return -1;
                }
            }
            i += 1;
        }
        buckets
    }
}
```
