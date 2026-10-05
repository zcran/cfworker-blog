---
title: "leetcode-双指针16"
date: 2026-09-05T09:53:01+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 轮转数组

给定一个整数数组 nums，将数组中的元素向右轮转 k 个位置，其中 k 是非负数。


```
impl Solution {
    /// 向右轮转数组 k 步（三次反转法）
    /// 时间复杂度 O(n)，空间复杂度 O(1)
    /// 1) 反转整个数组 2) 反转前 k 个 3) 反转剩余部分
    pub fn rotate(nums: &mut Vec<i32>, k: i32) {
        let n = nums.len();
        if n < 2 {
            return;
        }

        let k = k as usize % n;
        if k == 0 {
            return;
        }

        nums.reverse();
        nums[..k].reverse();
        nums[k..].reverse();
    }
}
```
