---
title: "leetcode-排序46"
date: 2026-10-05T00:35:02+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 两个数对之间的最大乘积差


两个数对 (a, b) 和 (c, d) 之间的 乘积差 定义为 (a * b) - (c * d) 。

例如，(5, 6) 和 (2, 7) 之间的乘积差是 (5 * 6) - (2 * 7) = 16 。

给你一个整数数组 nums ，选出四个 不同的 下标 w、x、y 和 z ，使数对 (nums[w], nums[x]) 和 (nums[y], nums[z]) 之间的 乘积差 取到 最大值 。

返回以这种方式取得的乘积差中的 最大值 。


```
impl Solution {
    /// 返回乘积差的最大值。
    ///
    /// 思路：
    /// 要使 (a * b) - (c * d) 最大，需要第一个数对取最大的两个数，
    /// 第二个数对取最小的两个数。排序后直接计算即可。
    pub fn max_product_difference(mut nums: Vec<i32>) -> i32 {
        nums.sort_unstable();

        let n = nums.len();
        nums[n - 1] * nums[n - 2] - nums[0] * nums[1]
    }
}
```
