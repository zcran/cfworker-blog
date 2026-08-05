---
title: "leetcode-滑动窗口65"
date: 2026-07-18T11:02:33+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 滑动子数组的美丽值

给你一个长度为 n 的整数数组 nums ，请你求出每个长度为 k 的子数组的 美丽值 。

一个子数组的 美丽值 定义为：如果子数组中第 x 小整数 是 负数 ，那么美丽值为第 x 小的数，否则美丽值为 0 。

请你返回一个包含 n - k + 1 个整数的数组，依次 表示数组中从第一个下标开始，每个长度为 k 的子数组的 美丽值 。

子数组指的是数组中一段连续 非空 的元素序列。


```
impl Solution {
    pub fn get_subarray_beauty(nums: Vec<i32>, k: i32, x: i32) -> Vec<i32> {
        let k = k as usize;
        let x = x as usize;
        let n = nums.len();
        let mut res = vec![0; n - k + 1];

        // 值域范围 [-50, 50]，偏移 50 映射到 [0, 100]
        const OFFSET: usize = 50;
        const RANGE: usize = 101;
        let mut freq = vec![0; RANGE];

        // 统计第一个窗口
        for i in 0..k {
            if nums[i] < 0 {
                freq[(nums[i] + OFFSET as i32) as usize] += 1;
            }
        }

        // 查找第 x 小的负数
        let find_xth_negative = |freq: &[i32]| -> i32 {
            let mut count = 0;
            for val in 0..OFFSET {
                count += freq[val];
                if count >= x as i32 {
                    return val as i32 - OFFSET as i32;
                }
            }
            0
        };

        // 处理每个窗口
        for i in 0..res.len() {
            if i > 0 {
                // 滑动窗口：移除左边界，添加右边界
                let left = nums[i - 1];
                let right = nums[i + k - 1];

                if left < 0 {
                    freq[(left + OFFSET as i32) as usize] -= 1;
                }
                if right < 0 {
                    freq[(right + OFFSET as i32) as usize] += 1;
                }
            }

            res[i] = find_xth_negative(&freq);
        }

        res
    }
}
```
