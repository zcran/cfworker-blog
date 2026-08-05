---
title: "leetcode-滑动窗口42"
date: 2026-07-18T11:02:31+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 最高频元素的频数

元素的 频数 是该元素在一个数组中出现的次数。

给你一个整数数组 nums 和一个整数 k 。在一步操作中，你可以选择 nums 的一个下标，并将该下标对应元素的值增加 1 。

执行最多 k 次操作后，返回数组中最高频元素的 最大可能频数 。


```
impl Solution {
    pub fn max_frequency(mut nums: Vec<i32>, k: i32) -> i32 {
        nums.sort_unstable();
        let k = k as i64;
        let mut left = 0;
        let mut sum = 0i64;
        let mut max_len = 0;

        for right in 0..nums.len() {
            let x = nums[right] as i64;
            sum += x;

            // 如果当前窗口内所有数变成 x 需要的操作数超过 k
            // 则移动左指针缩小窗口
            while (right - left + 1) as i64 * x - sum > k {
                sum -= nums[left] as i64;
                left += 1;
            }

            max_len = max_len.max(right - left + 1);
        }

        max_len as i32
    }
}
```
