---
title: "leetcode-双指针45"
date: 2026-09-05T09:53:03+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 区间子数组个数

给你一个整数数组 nums 和两个整数：left 及 right 。找出 nums 中连续、非空且其中最大元素在范围 [left, right] 内的子数组，并返回满足条件的子数组的个数。

生成的测试用例保证结果符合 32-bit 整数范围。


```
impl Solution {
    pub fn num_subarray_bounded_max(nums: Vec<i32>, left: i32, right: i32) -> i32 {
        let mut count = 0;
        let mut invalid = -1;  // 上一个 > right 的位置
        let mut valid = -1;    // 上一个 >= left 的位置

        for (i, &num) in nums.iter().enumerate() {
            let i = i as i32;

            if num > right {
                invalid = i;
            }
            if num >= left {
                valid = i;
            }

            // 以当前位置为结尾的有效子数组数量
            count += valid - invalid;
        }

        count
    }
}
```
