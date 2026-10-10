---
title: "leetcode-数学42"
date: 2026-10-07T20:39:31+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 可被三整除的偶数的平均值

给你一个由正整数组成的整数数组 nums ，返回其中可被 3 整除的所有偶数的平均值。

注意：n 个元素的平均值等于 n 个元素 求和 再除以 n ，结果 向下取整 到最接近的整数。


```
impl Solution {
    pub fn average_value(nums: Vec<i32>) -> i32 {
        // 可被 3 整除的偶数 <=> 可被 6 整除
        let (sum, cnt) = nums
            .into_iter()
            .filter(|n| n % 6 == 0)
            .fold((0, 0), |(sum, cnt), n| (sum + n, cnt + 1));

        // cnt 为 0 时无符合条件的元素，返回 0；否则返回向下取整的平均值
        if cnt == 0 { 0 } else { sum / cnt }
    }
}
```
