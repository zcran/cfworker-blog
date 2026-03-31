---
title: "leetcode-脑经急转弯9"
date: 2026-03-24T20:55:57+08:00
tags: ["leetcode", "脑筋急转弯"]
draft: false
---

## 按位与最大的最长子数组


给你一个长度为 n 的整数数组 nums 。

考虑 nums 中进行 按位与（bitwise AND）运算得到的值 最大 的 非空 子数组。

换句话说，令 k 是 nums 任意 子数组执行按位与运算所能得到的最大值。那么，只需要考虑那些执行一次按位与运算后等于 k 的子数组。
返回满足要求的 最长 子数组的长度。

数组的按位与就是对数组中的所有数字进行按位与运算。

子数组 是数组中的一个连续元素序列。

```
impl Solution {
    pub fn longest_subarray(nums: Vec<i32>) -> i32 {
        let (mut ans, mut m, mut len) = (0, i32::MIN, 0);
        for &i in nums.iter() {
            if i > m {
                m = i;
                ans = 1;
                len = 1;
            }
            else if i == m {
                len += 1;
                ans = ans.max(len);
            }else {
                len = 0;
            }
        }
        ans
    }
}

```
