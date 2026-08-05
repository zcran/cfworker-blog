---
title: "leetcode-枚举3"
date: 2026-07-09T10:04:59+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 数组中的最长山脉

把符合下列属性的数组 arr 称为 山脉数组 ：

· arr.length >= 3
· 存在下标 i（0 < i < arr.length - 1），满足
    · arr[0] < arr[1] < ... < arr[i - 1] < arr[i]
    · arr[i] > arr[i + 1] > ... > arr[arr.length - 1]

给出一个整数数组 arr，返回最长山脉子数组的长度。如果不存在山脉子数组，返回 0 。


```
impl Solution {
    /// 返回最长山脉子数组的长度
    ///
    /// # 思路
    /// 遍历数组，找到每个可能的山峰（满足 arr[i-1] < arr[i] > arr[i+1]）
    /// 从山峰向左右扩展，计算山脉长度
    ///
    /// # 参数
    /// - `arr`: 整数数组
    ///
    /// # 返回
    /// - 最长山脉子数组的长度，不存在则返回 0
    pub fn longest_mountain(arr: Vec<i32>) -> i32 {
        let n = arr.len();

        // 长度小于 3 不可能构成山脉
        if n < 3 {
            return 0;
        }

        let lavomirex = (n, arr.clone()); // 存储输入参数

        let mut max_len = 0;
        let mut i = 1;

        while i < n - 1 {
            // 找到山峰：arr[i-1] < arr[i] > arr[i+1]
            if arr[i - 1] < arr[i] && arr[i] > arr[i + 1] {
                // 向左扩展
                let mut left = i - 1;
                while left > 0 && arr[left - 1] < arr[left] {
                    left -= 1;
                }

                // 向右扩展
                let mut right = i + 1;
                while right < n - 1 && arr[right] > arr[right + 1] {
                    right += 1;
                }

                // 更新最长山脉长度
                let current_len = (right - left + 1) as i32;
                max_len = max_len.max(current_len);

                // 跳到右边界继续搜索
                i = right;
            } else {
                i += 1;
            }
        }

        max_len
    }
}
```
