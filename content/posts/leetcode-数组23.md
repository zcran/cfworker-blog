---
title: "leetcode-数组23"
date: 2026-10-10T20:38:42+08:00
tags: ["leetcode", "数组"]
draft: false
---


## 存在连续三个奇数的数组

给你一个整数数组 arr，请你判断数组中是否存在连续三个元素都是奇数的情况：如果存在，请返回 true ；否则，返回 false 。


```
impl Solution {
    /// 判断数组中是否存在连续三个奇数。
    ///
    /// 一次遍历，用 `count` 记录当前连续奇数的个数：
    /// 遇到奇数则加一，达到 3 立即返回 true；遇到偶数则清零。
    ///
    /// 时间复杂度 O(n)，空间复杂度 O(1)。
    pub fn three_consecutive_odds(arr: Vec<i32>) -> bool {
        let mut count = 0;

        for x in arr {
            if x & 1 == 1 {
                count += 1;
                if count == 3 {
                    return true;
                }
            } else {
                count = 0;
            }
        }

        false
    }
}
```
