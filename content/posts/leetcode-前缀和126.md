---
title: "leetcode-前缀和126"
date: 2026-08-25T10:02:54+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 最大平衡异或子数组的长度

给你一个整数数组 nums，返回同时满足以下两个条件的 最长子数组的长度 ：

1. 子数组的按位异或（XOR）为 0。

2. 子数组包含的 偶数 和 奇数 数量相等。

如果不存在这样的子数组，则返回 0。

子数组 是数组中的一个连续、非空 元素序列。


```
use std::collections::HashMap;

impl Solution {
    pub fn max_balanced_subarray(nums: Vec<i32>) -> i32 {
        let n = nums.len();
        let mut ans = 0;
        let mut xor_prefix = 0;
        let mut diff = n as i32; // 初始偏移，使得 diff 非负
        let mut pos = HashMap::new();

        // 空前缀: 位置 -1
        pos.insert((xor_prefix, diff), -1);

        for (i, &x) in nums.iter().enumerate() {
            xor_prefix ^= x;
            // 奇数 +1，偶数 -1
            diff += if x % 2 == 1 { 1 } else { -1 };

            let key = (xor_prefix, diff);
            if let Some(&prev) = pos.get(&key) {
                ans = ans.max((i as i32) - prev);
            } else {
                pos.insert(key, i as i32);
            }
        }

        ans
    }
}
```
