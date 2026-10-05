---
title: "leetcode-位运算60"
date: 2026-09-24T10:33:38+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 判断一个数组是否可以变为有序

给你一个下标从 0 开始且全是 正 整数的数组 nums 。

一次 操作 中，如果两个 相邻 元素在二进制下 设置位 的数目 相同 ，那么你可以将这两个元素交换。你可以执行这个操作 任意次 （也可以 0 次）。

如果你可以使数组变为非降序，请你返回 true ，否则返回 false 。


```
impl Solution {
    pub fn can_sort_array(nums: Vec<i32>) -> bool {
        // 只有设置位数相同的相邻元素才能交换，因此数组被“设置位数”分段：
        // 段内元素可任意重排，段间顺序固定。
        // 结论：能排成非降序 <=> 每一段的元素都能按升序填入其固定位置，
        // 即遍历时当前元素不能小于“上一段的元素最大值”。
        let mut prev_group_max = 0; // 上一段的最大值
        let mut cur_group_max = 0;  // 当前段的最大值
        let mut prev_bits = -1;     // 上一元素的设置位数

        for num in nums {
            let bits = num.count_ones() as i32;
            if bits == prev_bits {
                cur_group_max = cur_group_max.max(num);
            } else {
                // 进入新段：结算上一段，并检查当前元素是否小于上一段最大值
                prev_group_max = cur_group_max;
                cur_group_max = num;
                prev_bits = bits;
            }
            if num < prev_group_max {
                return false;
            }
        }
        true
    }
}
```
