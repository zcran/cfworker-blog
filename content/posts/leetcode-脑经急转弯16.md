---
title: "leetcode-脑经急转弯16"
date: 2026-03-24T20:55:57+08:00
tags: ["leetcode", "脑筋急转弯"]
draft: false
---


## 数组元素相等的最小操作次数

给你一个长度为 n 的整数数组 nums。

在一次操作中，可以选择任意子数组 nums[l...r] （0 <= l <= r < n），并将该子数组中的每个元素 替换 为所有元素的 按位与（bitwise AND）结果。

返回使数组 nums 中所有元素相等所需的最小操作次数。

子数组 是数组中连续的、非空的元素序列。

```
impl Solution {
    pub fn min_operations(nums: Vec<i32>) -> i32 {
        nums.windows(2)     //  创建大小为2的滑动窗口迭代器
            .any(|w| w[0] != w[1]) as i32     // 检查是否有任何窗口内元素不相等,并将bool转换为i32 (true→1, false→0)
    }
}
```
