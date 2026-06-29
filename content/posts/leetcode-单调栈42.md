---
title: "leetcode-单调栈42"
date: 2026-06-07T10:14:05+08:00
tags: ["leetcode", "单调栈"]
draft: false
---


## 美丽塔 II

给你一个长度为 n 下标从 0 开始的整数数组 maxHeights 。

你的任务是在坐标轴上建 n 座塔。第 i 座塔的下标为 i ，高度为 heights[i] 。

如果以下条件满足，我们称这些塔是 美丽 的：

1.1 <= heights[i] <= maxHeights[i]
2.heights 是一个 山脉 数组。

如果存在下标 i 满足以下条件，那么我们称数组 heights 是一个 山脉 数组：

· 对于所有 0 < j <= i ，都有 heights[j - 1] <= heights[j]
· 对于所有 i <= k < n - 1 ，都有 heights[k + 1] <= heights[k]

请你返回满足 美丽塔 要求的方案中，高度和的最大值 。


```
impl Solution {
    /// 计算最大山峰数组和
    ///
    /// 思路：枚举每个位置作为峰顶，左边非递减，右边非递增，每个数不超过原值
    /// 使用单调栈 O(n) 计算每个峰顶的左右最大和
    pub fn maximum_sum_of_heights(a: Vec<i32>) -> i64 {
        let n = a.len();
        let mut stack = Vec::with_capacity(n);

        // right[i]: 以 i 为峰顶时，右侧部分的最大和（包含 i）
        let mut right = vec![0; n];

        // 从右向左计算右侧和
        for i in (0..n).rev() {
            let x = a[i] as i64;
            // 维护单调递增栈，弹出 >= 当前值的索引
            while let Some(&j) = stack.last() {
                if a[j] < a[i] { break; }
                stack.pop();
            }
            // 右侧和 = 当前值 * 区间长度 + 右侧更小值的和
            right[i] = if let Some(&j) = stack.last() {
                x * (j - i) as i64 + right[j]
            } else {
                x * (n - i) as i64
            };
            stack.push(i);
        }

        // 计算左侧和并同步更新答案
        let mut ans = 0;
        let mut left = vec![0; n];
        stack.clear();

        for i in 0..n {
            let x = a[i] as i64;
            // 维护单调递增栈
            while let Some(&j) = stack.last() {
                if a[j] < a[i] { break; }
                stack.pop();
            }
            // 左侧和 = 当前值 * 区间长度 + 左侧更小值的和
            left[i] = if let Some(&j) = stack.last() {
                x * (i - j) as i64 + left[j]
            } else {
                x * (i + 1) as i64
            };
            stack.push(i);
            // 峰顶被左右各算一次，减去重复
            ans = ans.max(left[i] + right[i] - x);
        }

        ans
    }
}
```
