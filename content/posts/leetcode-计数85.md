---
title: "leetcode-计数85"
date: 2026-08-01T10:40:55+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 删除数对后的最小数组长度

给你一个下标从 0 开始的 非递减 整数数组 nums 。

你可以执行以下操作任意次：

· 选择 两个 下标 i 和 j ，满足 nums[i] < nums[j] 。

· 将 nums 中下标在 i 和 j 处的元素删除。剩余元素按照原来的顺序组成新的数组，下标也重新从 0 开始编号。

请你返回一个整数，表示执行以上操作任意次后（可以执行 0 次），nums 数组的 最小 数组长度。




```
impl Solution {
    pub fn min_length_after_removals(nums: Vec<i32>) -> i32 {
        let n = nums.len() as i32;

        // 数组非递减，相同元素连续，统计最大出现次数（众数频率）
        let mut max_freq = 1;
        let mut curr_freq = 1;
        for i in 1..nums.len() {
            if nums[i] == nums[i - 1] {
                curr_freq += 1;
                if curr_freq > max_freq {
                    max_freq = curr_freq;
                }
            } else {
                curr_freq = 1;
            }
        }

        // 最终剩余长度 = max(n % 2, 2 * max_freq - n)
        // 当众数频率不超过一半时，可全部配对，剩余 n 的奇偶性
        // 否则，剩余元素全为众数，数量为 2*max_freq - n
        (n % 2).max(2 * max_freq - n)
    }
}
```
