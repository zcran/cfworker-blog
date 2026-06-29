---
title: "leetcode-单调栈16"
date: 2026-06-07T10:14:04+08:00
tags: ["leetcode", "单调栈"]
draft: false
---


## 子数组的最小值之和

给定一个整数数组 arr，找到 min(b) 的总和，其中 b 的范围为 arr 的每个（连续）子数组。

由于答案可能很大，因此 返回答案模 10^9 + 7 。

```
impl Solution {
    /// 子数组最小值之和
    ///
    /// 核心思路：单调栈 + 贡献法
    /// - 每个元素作为最小值能贡献的子数组数 = (左侧更大长度+1) * (右侧更大长度+1)
    /// - 使用单调栈找到左右两侧第一个更小元素的边界
    /// - 注意边界处理：左侧严格大于，右侧大于等于（避免重复计算相同值）
    pub fn sum_subarray_mins(arr: Vec<i32>) -> i32 {
        let n = arr.len();
        let mod_val = 1_000_000_007;

        // 1. 计算左侧边界：左边第一个小于当前值的位置
        let mut left = vec![0; n];
        let mut stack = Vec::with_capacity(n);
        for i in 0..n {
            // 严格大于才弹出（保证最小值唯一性）
            while let Some(&top) = stack.last() && arr[top] > arr[i] {
                stack.pop();
            }
            // 左侧边界：栈空则到 -1，否则到栈顶位置
            left[i] = *stack.last().unwrap_or(&usize::MAX) as i32;
            stack.push(i);
        }

        // 2. 计算右侧边界：右边第一个小于等于当前值的位置
        let mut right = vec![0; n];
        stack.clear();
        for i in (0..n).rev() {
            // 大于等于才弹出（与左侧形成半开区间，避免重复）
            while let Some(&top) = stack.last() && arr[top] >= arr[i] {
                stack.pop();
            }
            // 右侧边界：栈空则到 n，否则到栈顶位置
            right[i] = *stack.last().unwrap_or(&n) as i32;
            stack.push(i);
        }

        // 3. 计算每个元素的贡献
        let mut ans: i64 = 0;
        for i in 0..n {
            let left_len = (i as i32 - left[i]) as i64;      // 左侧可扩展数量
            let right_len = (right[i] - i as i32) as i64;    // 右侧可扩展数量
            let contribution = left_len * right_len * (arr[i] as i64);
            ans = (ans + contribution) % mod_val as i64;
        }

        ans as i32
    }
}
```
