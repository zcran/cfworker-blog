---
title: "leetcode-动态规划12"
date: 2026-10-05T20:39:54+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 乘积最大子数组

给你一个整数数组 nums ，请你找出数组中乘积最大的非空连续 子数组（该子数组中至少包含一个数字），并返回该子数组所对应的乘积。

测试用例的答案是一个 32-位 整数。

请注意，一个只包含一个元素的数组的乘积是这个元素的值。


```
impl Solution {
    pub fn max_product(nums: Vec<i32>) -> i32 {
        let mut ans = i32::MIN;
        let mut f_max = 1; // 以当前元素结尾的子数组的最大乘积
        let mut f_min = 1; // 以当前元素结尾的子数组的最小乘积

        for x in nums {
            // 遇到负数时，最大与最小乘积互换角色
            if x < 0 {
                std::mem::swap(&mut f_max, &mut f_min);
            }

            // 单独成段（x 本身）或接在前一子数组之后
            f_max = x.max(f_max * x);
            f_min = x.min(f_min * x);

            ans = ans.max(f_max);
        }

        ans
    }
}
```
