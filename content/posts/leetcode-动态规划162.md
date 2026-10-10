---
title: "leetcode-动态规划162"
date: 2026-10-05T20:40:05+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 替换至多一个元素后最长非递减子数组

给你一个整数数组 nums。

你被允许 最多 将数组中的一个元素替换为任何其他整数值。

返回在执行至多一次替换后，可以获得的 最长非递减子数组 的长度。

子数组 是数组中的一段连续的元素序列。

如果数组中的每个元素都大于或等于其前一个元素（如果存在），则称该数组为 非递减 的。


```
impl Solution {
    pub fn longest_subarray(nums: Vec<i32>) -> i32 {
        let n = nums.len();
        if n <= 1 {
            return n as i32;
        }

        // f0：以当前元素结尾、未使用替换的最长非递减子数组长度
        // f1：以当前元素结尾、已使用替换的最长非递减子数组长度
        // pre0：以上一个元素结尾的 f0（用于计算跨一个元素的替换情况）
        let mut f0 = 1;
        let mut f1 = 1;
        let mut pre0 = 0;
        let mut ans = 1;

        for i in 1..n {
            let old_f0 = f0;

            if nums[i - 1] <= nums[i] {
                f0 += 1;
                f1 += 1;
            } else {
                f0 = 1;
                f1 = 0; // 重置为 0，随后下面会更新为至少 2 或更优
            }

            // 替换 nums[i-1] 使得 nums[i-2] <= nums[i]
            let replace_prev = if i >= 2 && nums[i - 2] <= nums[i] {
                pre0 + 2
            } else {
                2
            };
            f1 = f1.max(replace_prev);

            ans = ans.max(old_f0 + 1).max(f1);
            pre0 = old_f0;
        }

        ans
    }
}
```
