---
title: "leetcode-单调栈7"
date: 2026-06-07T10:14:04+08:00
tags: ["leetcode", "单调栈"]
draft: false
---


## 132 模式

给你一个整数数组 nums ，数组中共有 n 个整数。132 模式的子序列 由三个整数 nums[i]、nums[j] 和 nums[k] 组成，并同时满足：i < j < k 和 nums[i] < nums[k] < nums[j] 。

如果 nums 中存在 132 模式的子序列 ，返回 true ；否则，返回 false 。

```
impl Solution {
    /// 寻找 132 模式（i < j < k 且 nums[i] < nums[k] < nums[j]）
    ///
    /// 核心思路：从右向左维护可能的 "2" 值（第三个数），
    /// 同时用单调递减栈维护可能的 "3" 值（中间数）
    pub fn find132pattern(nums: Vec<i32>) -> bool {
        let mut third = i32::MIN;  // 候选的 "2"（132 中的 2）
        let mut stack = vec![];     // 单调递减栈，存储候选的 "3"

        // 从右向左遍历，寻找 nums[i] < third
        for &num in nums.iter().rev() {
            // 如果当前数字小于 third，说明找到了 132 模式
            if num < third {
                return true;
            }

            // 维护单调递减栈：当 num > 栈顶时，弹出栈顶并更新 third
            while let Some(&top) = stack.last() {
                if num > top {
                    third = stack.pop().unwrap();  // 栈顶作为新的 "2"
                } else {
                    break;
                }
            }

            // 当前数字作为候选的 "3" 入栈
            stack.push(num);
        }

        false
    }
}
```
