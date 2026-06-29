---
title: "leetcode-单调栈9"
date: 2026-06-07T10:14:04+08:00
tags: ["leetcode", "单调栈"]
draft: false
---


## 下一个更大元素 II


给定一个循环数组 nums （ nums[nums.length - 1] 的下一个元素是 nums[0] ），返回 nums 中每个元素的 下一个更大元素 。

数字 x 的 下一个更大的元素 是按数组遍历顺序，这个数字之后的第一个比它更大的数，这意味着你应该循环地搜索它的下一个更大的数。如果不存在，则输出 -1 。

```
impl Solution {
    /// 寻找循环数组中的下一个更大元素
    ///
    /// 核心思路：单调递减栈 + 循环数组处理（遍历两遍）
    /// - 遍历 2n-1 个元素，通过 i % n 实现循环访问
    /// - 栈中存储的是未找到下一个更大元素的下标
    /// - 遇到更大元素时，依次弹出栈中所有比它小的元素
    pub fn next_greater_elements(nums: Vec<i32>) -> Vec<i32> {
        let n = nums.len();
        let mut ans = vec![-1; n];           // 预分配结果数组，默认-1
        let mut stack = Vec::with_capacity(n); // 单调递减栈，存储下标

        // 遍历两倍长度（模拟循环数组），只需 2n-1 次
        for i in 0..2 * n - 1 {
            let curr_idx = i % n;             // 循环索引
            let curr_val = nums[curr_idx];    // 当前值

            // 栈非空且栈顶元素 < 当前值：找到了栈顶的下一个更大元素
            while let Some(&top_idx) = stack.last() {
                if nums[top_idx] >= curr_val {
                    break;  // 保持单调递减，停止弹出
                }

                // 弹出栈顶并记录答案
                ans[stack.pop().unwrap()] = curr_val;
            }

            // 只在前 n 个元素时入栈，避免重复入栈造成栈过大
            if i < n {
                stack.push(curr_idx);
            }
        }

        ans
    }
}
```
