---
title: "leetcode-模拟39"
date: 2026-08-08T11:31:10+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 获取生成数组中的最大值

给你一个整数 n 。按下述规则生成一个长度为 n + 1 的数组 nums ：

nums[0] = 0
nums[1] = 1
当 2 <= 2 * i <= n 时，nums[2 * i] = nums[i]
当 2 <= 2 * i + 1 <= n 时，nums[2 * i + 1] = nums[i] + nums[i + 1]

返回生成数组 nums 中的 最大 值。


```
impl Solution {
    pub fn get_maximum_generated(n: i32) -> i32 {
        if n <= 1 {
            return n;
        }

        let n = n as usize;
        let mut nums = vec![0; n + 1];
        nums[1] = 1;
        let mut max_val = 1;

        for i in 2..=n {
            // 偶数：nums[i] = nums[i/2]
            // 奇数：nums[i] = nums[i/2] + nums[i/2 + 1]
            nums[i] = if i & 1 == 0 {
                nums[i >> 1]
            } else {
                nums[i >> 1] + nums[(i >> 1) + 1]
            };
            max_val = max_val.max(nums[i]);
        }

        max_val
    }
}
```
