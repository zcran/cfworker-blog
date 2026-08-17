---
title: "leetcode-计数92"
date: 2026-08-01T10:40:56+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 分割数组

给你一个长度为 偶数 的整数数组 nums 。你需要将这个数组分割成 nums1 和 nums2 两部分，要求：

nums1.length == nums2.length == nums.length / 2 。
nums1 应包含 互不相同 的元素。
nums2也应包含 互不相同 的元素。

如果能够分割数组就返回 true ，否则返回 false 。


```
impl Solution {
    pub fn is_possible_to_split(mut nums: Vec<i32>) -> bool {
        // 核心洞察：一个数字最多出现2次（nums1和nums2各放一次）
        // 排序后检查是否有连续3个相同数字即可
        nums.sort_unstable();

        nums.windows(3)
            .all(|w| !(w[0] == w[1] && w[1] == w[2]))
    }
}
```
