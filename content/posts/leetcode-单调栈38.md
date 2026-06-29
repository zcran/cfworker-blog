---
title: "leetcode-单调栈38"
date: 2026-06-07T10:14:05+08:00
tags: ["leetcode", "单调栈"]
draft: false
---


## 下一个更大元素 IV

给你一个下标从 0 开始的非负整数数组 nums 。对于 nums 中每一个整数，你必须找到对应元素的 第二大 整数。

如果 nums[j] 满足以下条件，那么我们称它为 nums[i] 的 第二大 整数：

· j > i
· nums[j] > nums[i]
· 恰好存在 一个 k 满足 i < k < j 且 nums[k] > nums[i] 。

如果不存在 nums[j] ，那么第二大整数为 -1 。

· 比方说，数组 [1, 2, 4, 3] 中，1 的第二大整数是 4 ，2 的第二大整数是 3 ，3 和 4 的第二大整数是 -1 。

请你返回一个整数数组 answer ，其中 answer[i]是 nums[i] 的第二大整数。

```
impl Solution {
    /// 寻找每个元素右侧的第二个更大元素
    ///
    /// # 示例
    /// ```
    /// let nums = vec![2, 4, 0, 9, 6];
    /// let result = Solution::second_greater_element(nums);
    /// assert_eq!(result, vec![9, 6, 6, -1, -1]);
    /// ```
    pub fn second_greater_element(nums: Vec<i32>) -> Vec<i32> {
        let n = nums.len();
        let mut ans = vec![-1; n];
        let mut first = Vec::new();   // 等待第一次更大的索引
        let mut second = Vec::new();  // 等待第二次更大的索引

        for (i, &x) in nums.iter().enumerate() {
            // 当前元素作为第二次更大：更新结果
            while let Some(&j) = second.last() {
                if x > nums[j] {
                    ans[j] = x;
                    second.pop();
                } else {
                    break;
                }
            }

            // 收集所有首次更大的元素
            let mut found = Vec::new();
            while let Some(&j) = first.last() {
                if x > nums[j] {
                    found.push(j);
                    first.pop();
                } else {
                    break;
                }
            }

            // 刚找到首次更大的元素，现在等待第二次更大
            second.extend(found.into_iter().rev());

            first.push(i);
        }

        ans
    }
}
```
