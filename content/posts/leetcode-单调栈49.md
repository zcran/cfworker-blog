---
title: "leetcode-单调栈49"
date: 2026-06-07T10:14:06+08:00
tags: ["leetcode", "单调栈"]
draft: false
---


## 碗子数组的数目

给你一个整数数组 nums，包含 互不相同 的元素。

nums 的一个子数组 nums[l...r] 被称为 碗（bowl），如果它满足以下条件：

· 子数组的长度至少为 3。也就是说，r - l + 1 >= 3。
· 其两端元素的 最小值 严格大于 中间所有元素的 最大值。也就是说，min(nums[l], nums[r]) > max(nums[l + 1], ..., nums[r - 1])。

返回 nums 中 碗 子数组的数量。

子数组 是数组中连续的元素序列。

```
impl Solution {
    /// 计算「碗」子数组的数量
    /// 碗：长度至少为3，且两端的最小值严格大于中间所有元素的最大值
    pub fn bowl_subarrays(nums: Vec<i32>) -> i64 {
        let n = nums.len();
        let mut ans = 0i64;
        // 单调递减栈，存储元素索引
        let mut stack: Vec<usize> = Vec::with_capacity(n);

        for i in 0..n {
            let x = nums[i];
            // 弹出所有小于当前值的栈顶元素
            // 对于每个弹出的 j，当前 i 就是右侧第一个大于 nums[j] 的元素
            while let Some(&top_idx) = stack.last() {
                if nums[top_idx] < x {
                    // j = top_idx, 检查长度条件
                    if i - top_idx > 1 {
                        ans += 1;
                    }
                    stack.pop();
                } else {
                    break;
                }
            }
            // 检查栈顶（左侧第一个大于等于当前值的元素）
            if let Some(&left_idx) = stack.last() {
                if i - left_idx > 1 {
                    ans += 1;
                }
            }
            stack.push(i);
        }

        ans
    }
}
```
