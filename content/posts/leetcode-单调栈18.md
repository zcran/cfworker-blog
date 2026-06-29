---
title: "leetcode-单调栈18"
date: 2026-06-07T10:14:04+08:00
tags: ["leetcode", "单调栈"]
draft: false
---


##  奇偶跳

给定一个整数数组 arr，你可以从某一起始索引出发，跳跃一定次数。在你跳跃的过程中，第 1、3、5... 次跳跃称为奇数跳跃，而第 2、4、6... 次跳跃称为偶数跳跃。

你可以按以下方式从索引 i 向后跳转到索引 j（其中 i < j）：

· 在进行奇数跳跃时（如，第 1，3，5... 次跳跃），你将会跳到索引 j，使得 arr[i] <= arr[j]，且 arr[j] 的值尽可能小。如果存在多个这样的索引 j，你只能跳到满足要求的最小索引 j 上。
· 在进行偶数跳跃时（如，第 2，4，6... 次跳跃），你将会跳到索引 j，使得 arr[i] >= arr[j]，且 arr[j] 的值尽可能大。如果存在多个这样的索引 j，你只能跳到满足要求的最小索引 j 上。
· （对于某些索引 i，可能无法进行合乎要求的跳跃。）

如果从某一索引开始跳跃一定次数（可能是 0 次或多次），就可以到达数组的末尾（索引 arr.length - 1），那么该索引就会被认为是好的起始索引。

返回好的起始索引的数量。

```
use std::collections::HashMap;

impl Solution {
    /// 奇偶跳问题
    ///
    /// 核心思路：动态规划 + 有序集合（通过有序数组 + 二分查找模拟）
    ///
    /// 定义状态：
    /// - dp[i][0]：从索引 i 出发，第 1 次是奇数跳，能否到达末尾（1 表示能，0 表示不能）
    /// - dp[i][1]：从索引 i 出发，第 1 次是偶数跳，能否到达末尾
    ///
    /// 转移方程：
    /// - 奇数跳：跳到右边 >= arr[i] 的最小值位置 j → dp[i][0] = dp[j][1]
    /// - 偶数跳：跳到右边 <= arr[i] 的最大值位置 j → dp[i][1] = dp[j][0]
    ///
    /// 实现技巧：从右向左遍历，维护已遍历元素的升序列表，通过二分查找找到目标位置
    pub fn odd_even_jumps(arr: Vec<i32>) -> i32 {
        let n = arr.len();
        // dp[i][0] = 奇数跳结果, dp[i][1] = 偶数跳结果
        let mut dp = vec![[0, 0]; n];
        dp[n - 1] = [1, 1];  // 最后一步已经到达末尾

        // 存储已遍历元素的值到索引的映射
        let mut val_to_idx = HashMap::new();
        val_to_idx.insert(arr[n - 1], n - 1);

        // 维护已遍历元素的升序列表，用于二分查找
        let mut sorted_vals = vec![arr[n - 1]];

        let mut result = 1;  // 最后一个元素算一个有效起点

        // 从右向左遍历（倒数第二个到第一个）
        for i in (0..n - 1).rev() {
            // 二分查找：找右边 >= arr[i] 的最小值（奇数跳目标）
            let mut left = 0;
            let mut right = sorted_vals.len() - 1;
            let mut right_min = -1;  // 右边 >= 当前值的候选
            let mut left_max = -1;   // 右边 <= 当前值的候选

            while left <= right {
                let mid = left + (right - left) / 2;
                if sorted_vals[mid] > arr[i] {
                    right_min = sorted_vals[mid];   // 记录可能的更大值
                    if mid == 0 { break; }
                    right = mid - 1;
                } else if sorted_vals[mid] < arr[i] {
                    left_max = sorted_vals[mid];    // 记录可能的更小值
                    left = mid + 1;
                } else {
                    // 找到相等值，既是奇数跳目标也是偶数跳目标
                    left_max = sorted_vals[mid];
                    right_min = sorted_vals[mid];
                    break;
                }
            }

            // 处理边界：如果二分结束位置仍小于当前值，需要右移一位
            let insert_pos = if sorted_vals[mid] < arr[i] { mid + 1 } else { mid };

            // 根据找到的候选值查询 dp 状态
            let odd_jump = if right_min != -1 {
                dp[*val_to_idx.get(&right_min).unwrap()][1]  // 奇数跳后是偶数跳
            } else {
                0
            };

            let even_jump = if left_max != -1 {
                dp[*val_to_idx.get(&left_max).unwrap()][0]  // 偶数跳后是奇数跳
            } else {
                0
            };

            // 记录当前节点的 dp 值
            dp[i] = [odd_jump, even_jump];

            // 更新结果：奇数跳能到达末尾的起点计数
            result += odd_jump;

            // 将当前值插入到有序列表中，保持升序
            sorted_vals.insert(insert_pos, arr[i]);
            val_to_idx.insert(arr[i], i);
        }

        result
    }
}
```
