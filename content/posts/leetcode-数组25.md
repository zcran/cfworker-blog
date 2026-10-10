---
title: "leetcode-数组25"
date: 2026-10-10T20:38:42+08:00
tags: ["leetcode", "数组"]
draft: false
---


## 检查数组是否经排序和轮转得到

给你一个数组 nums 。nums 的源数组中，所有元素与 nums 相同，但按非递减顺序排列。

如果 nums 能够由源数组轮转若干位置（包括 0 个位置）得到，则返回 true ；否则，返回 false 。

源数组中可能存在 重复项 。

注意：数组 A 在轮转 x 个位置后得到长度相同的数组 B ，使得对于每一个有效的下标 i，满足 B[i] == A[(i+x) % A.length]。


```
impl Solution {
    /// 判断数组是否由非递减源数组轮转若干位置得到。
    ///
    /// 轮转后的非递减数组最多只有一处「下降」（即 `nums[i] > nums[i+1]`），
    /// 该下降位于轮转断点处。遍历数组（含末尾到首位的环形比较），
    /// 若下降次数超过 1，则不是轮转结果。
    ///
    /// 时间复杂度 O(n)，空间复杂度 O(1)。
    pub fn check(nums: Vec<i32>) -> bool {
        let n = nums.len();
        let mut drops = 0;

        for i in 0..n {
            if nums[i] > nums[(i + 1) % n] {
                drops += 1;
                if drops > 1 {
                    return false;
                }
            }
        }

        true
    }
}
```
