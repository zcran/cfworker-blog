---
title: "leetcode-数学86"
date: 2026-10-07T20:39:34+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 构造奇偶一致的数组 I

给你一个长度为 n 的数组 nums1，其中包含 互不相同 的整数。

你需要构造另一个长度为 n 的数组 nums2，使得 nums2 中的元素要么全部为 奇数，要么全部为 偶数。

对于每个下标 i，你必须从以下两种选择中 任选其一（顺序不限）：

nums2[i] = nums1[i]
nums2[i] = nums1[i] - nums1[j]，其中 j != i

如果能够构造出满足条件的数组，则返回 true；否则，返回 false。


```
Solution {
    pub fn uniform_array(nums1: Vec<i32>) -> bool {
        true
    }
}
```
