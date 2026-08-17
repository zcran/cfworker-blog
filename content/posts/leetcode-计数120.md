---
title: "leetcode-计数120"
date: 2026-08-01T10:40:57+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 避免禁用值的最小交换次数

给你两个长度为 n 的整数数组 nums 和 forbidden。

你可以执行以下操作任意次（包括零次）：

选择两个 不同 下标 i 和 j，然后交换 nums[i] 和 nums[j]。
返回使得对于每个下标 i，nums[i] 不等于 forbidden[i] 所需的 最小 交换次数。如果无论如何都无法满足条件，返回 -1。




```
impl Solution {
    /// 返回使 nums[i] != forbidden[i] 对所有 i 成立的最小交换次数。
    /// 若无法完成，返回 -1。
    pub fn min_swaps(nums: Vec<i32>, forbidden: Vec<i32>) -> i32 {
        let n = nums.len();

        /// Boyer-Moore 投票算法：返回 (众数出现次数, 众数值)
        fn majority(arr: &[i32]) -> (usize, i32) {
            let mut cand = 0;
            let mut cnt = 0;
            for &x in arr {
                if cnt == 0 {
                    cand = x;
                    cnt = 1;
                } else if x == cand {
                    cnt += 1;
                } else {
                    cnt -= 1;
                }
            }
            let maj_cnt = arr.iter().filter(|&&x| x == cand).count();
            (maj_cnt, cand)
        }

        // 检查全局可行性：合并数组中不能有元素出现超过 n 次
        let mut merged = Vec::with_capacity(n * 2);
        merged.extend_from_slice(&nums);
        merged.extend_from_slice(&forbidden);
        let (global_maj_cnt, _) = majority(&merged);
        if global_maj_cnt > n {
            return -1;
        }

        // 收集冲突位置（nums[i] == forbidden[i]）的值
        let conflicts: Vec<i32> = nums.iter()
            .zip(&forbidden)
            .filter(|(a, b)| a == b)
            .map(|(&a, _)| a)
            .collect();

        let conflict_cnt = conflicts.len();
        if conflict_cnt == 0 {
            return 0;
        }

        let (maj_cnt, _) = majority(&conflicts);

        // 若冲突中某值占多数，需将其全部换出；否则两两交换即可
        if maj_cnt > conflict_cnt / 2 {
            maj_cnt as i32
        } else {
            ((conflict_cnt + 1) / 2) as i32
        }
    }
}
```
