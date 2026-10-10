---
title: "leetcode-哈希表77"
date: 2026-10-09T20:08:00+08:00
tags: ["leetcode", "哈希表"]
draft: false
---


## 找出最大的几近缺失整数

给你一个整数数组 nums 和一个整数 k 。

如果整数 x 恰好仅出现在 nums 中的一个大小为 k 的子数组中，则认为 x 是 nums 中的几近缺失（almost missing）整数。

返回 nums 中 最大的几近缺失 整数，如果不存在这样的整数，返回 -1 。

子数组 是数组中的一个连续元素序列。


```
impl Solution {
    pub fn largest_integer(nums: Vec<i32>, k: i32) -> i32 {
        let n = nums.len();
        let k = k as usize;

        // 若 k == n，整个数组就是唯一的 k 长子数组，所有元素都属于它
        if k == n {
            return *nums.iter().max().unwrap();
        }

        // 统计每个值在 nums 中出现的次数（题设值域 1..=50）
        let mut count = [0i32; 51];
        for &x in &nums {
            count[x as usize] += 1;
        }

        // k == 1 时，每个单个元素就是一个子数组，
        // "仅出现在一个 k 长子数组" 等价于 "在整个数组中只出现一次"
        if k == 1 {
            for v in (1..=50i32).rev() {
                if count[v as usize] == 1 {
                    return v;
                }
            }
            return -1;
        }

        // k > 1 时，只有首尾元素可能只属于一个 k 长子数组，
        // 中间元素会被多个长度 k 的子数组覆盖
        let mut ans = -1;
        if count[nums[0] as usize] == 1 {
            ans = ans.max(nums[0]);
        }
        if count[nums[n - 1] as usize] == 1 {
            ans = ans.max(nums[n - 1]);
        }

        ans
    }
}
```
