---
title: "leetcode-排序8"
date: 2026-10-05T00:34:59+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 第三大的数

给定一个整数数组 nums。

返回此数组中 第三大的数 。如果不存在，则返回数组中 最大 的数。


```
impl Solution {
    /// 返回数组中第三大的数；若不存在第三大的数，则返回最大的数。
    ///
    /// 思路：用三个变量 a > b > c 维护「前三大的不同值」，
    /// 遍历中按大小关系依次下移更新，天然去重。
    /// 用 i64 作为初值哨兵，避免与 i32::MIN 冲突。
    /// 时间 O(n)，额外空间 O(1)。
    pub fn third_max(nums: Vec<i32>) -> i32 {
        let (mut a, mut b, mut c) = (i64::MIN, i64::MIN, i64::MIN);

        for x in nums {
            let x = x as i64;
            // 依次与 a、b、c 比较；相等的情况自然被各分支的严格大于排除
            if x > a {
                c = b;
                b = a;
                a = x;
            } else if x > b && x < a {
                c = b;
                b = x;
            } else if x > c && x < b {
                c = x;
            }
        }

        // c 仍为哨兵，说明不足三个不同值，返回最大值
        if c == i64::MIN { a as i32 } else { c as i32 }
    }
}
```
