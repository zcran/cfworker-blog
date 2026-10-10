---
title: "leetcode-数组16"
date: 2026-10-10T20:38:41+08:00
tags: ["leetcode", "数组"]
draft: false
---


## 将每个元素替换为右侧最大元素

给你一个数组 arr ，请你将每个元素用它右边最大的元素替换，如果是最后一个元素，用 -1 替换。

完成所有替换操作后，请你返回这个数组。


```
impl Solution {
    /// 将每个元素替换为其右侧所有元素的最大值，最后一个元素替换为 -1。
    ///
    /// 从右向左遍历，用 `max_right` 维护当前位置右侧的最大值：
    /// 先记录当前答案，再用当前元素更新 `max_right`。
    ///
    /// 时间复杂度 O(n)，空间复杂度 O(n)（结果数组）。
    pub fn replace_elements(arr: Vec<i32>) -> Vec<i32> {
        let n = arr.len();
        let mut ans = vec![-1; n];
        let mut max_right = -1;

        for i in (0..n).rev() {
            ans[i] = max_right;
            max_right = max_right.max(arr[i]);
        }

        ans
    }
}
```
