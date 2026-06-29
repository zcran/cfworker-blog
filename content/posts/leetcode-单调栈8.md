---
title: "leetcode-单调栈8"
date: 2026-06-07T10:14:04+08:00
tags: ["leetcode", "单调栈"]
draft: false
---


## 下一个更大元素 I

nums1 中数字 x 的 下一个更大元素 是指 x 在 nums2 中对应位置 右侧 的 第一个 比 x 大的元素。

给你两个 没有重复元素 的数组 nums1 和 nums2 ，下标从 0 开始计数，其中nums1 是 nums2 的子集。

对于每个 0 <= i < nums1.length ，找出满足 nums1[i] == nums2[j] 的下标 j ，并且在 nums2 确定 nums2[j] 的 下一个更大元素 。如果不存在下一个更大元素，那么本次查询的答案是 -1 。

返回一个长度为 nums1.length 的数组 ans 作为答案，满足 ans[i] 是如上所述的 下一个更大元素 。

```
use std::collections::HashMap;

impl Solution {
    /// 寻找下一个更大元素
    ///
    /// 核心思路：单调递减栈 + 哈希映射
    /// - 栈中维护尚未找到下一个更大元素的数（单调递减）
    /// - 遇到更大元素时，依次弹出栈中所有比它小的数，并记录答案
    pub fn next_greater_element(nums1: Vec<i32>, nums2: Vec<i32>) -> Vec<i32> {
        let mut ans = vec![-1; nums1.len()];           // 预分配结果数组，默认-1
        let mut stack = Vec::with_capacity(nums2.len()); // 单调递减栈，预分配容量

        // 建立 nums1 的值到索引的映射，O(1) 查找
        let pos_map: HashMap<_, _> = nums1
            .iter()
            .enumerate()
            .map(|(i, &val)| (val, i))
            .collect();

        // 遍历 nums2，维护单调递减栈
        for &num in &nums2 {
            // 当前元素大于栈顶时，说明栈顶找到了下一个更大元素
            while let Some(&top) = stack.last() {
                if top >= num {
                    break;  // 栈保持递减，停止弹出
                }

                // 栈顶元素出栈，并记录答案
                let popped = stack.pop().unwrap();
                if let Some(&idx) = pos_map.get(&popped) {
                    ans[idx] = num;
                }
            }
            stack.push(num);  // 当前元素入栈
        }

        ans  // 栈中剩余元素保持默认值 -1（没有下一个更大元素）
    }
}
```
