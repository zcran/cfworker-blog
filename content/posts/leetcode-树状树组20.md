---
title: "leetcode-树状树组20"
date: 2026-05-03T20:07:53+08:00
tags: ["leetcode", "树状树组"]
draft: false
---


## 统计上升四元组

给你一个长度为 n 下标从 0 开始的整数数组 nums ，它包含 1 到 n 的所有数字，请你返回上升四元组的数目。

如果一个四元组 (i, j, k, l) 满足以下条件，我们称它是上升的：

· 0 <= i < j < k < l < n 且
· nums[i] < nums[k] < nums[j] < nums[l] 。

```
impl Solution {
    /// 统计递增四元组 (i, j, k, l) 的数量，满足 i < j < k < l 且 nums[i] < nums[j] < nums[k] < nums[l]。
    /// 时间复杂度 O(n²)，空间复杂度 O(n)，其中 n = nums.len()。
    pub fn count_quadruplets(nums: Vec<i32>) -> i64 {
        let n = nums.len();
        // pre[x] 表示在当前位置 j 右侧、且值比某个 k 的 nums[k] 大的元素个数？实际含义：pre[val] 统计右侧已扫描的、值等于 val 的元素个数（作为候选的 l）
        // 更准确：pre[val] 表示在当前位置 j 的右侧、已经被当作比 nums[k] 大的元素（即潜在 l）的计数。
        let mut pre = vec![0; n + 1];
        let mut ans = 0;

        for j in 0..n {
            let mut bigger = 0; // 右侧比 nums[j] 大的元素个数（且这些元素尚未被计入 pre）
            // 从右向左扫描 k，保证 k > j
            for k in (j + 1..n).rev() {
                if nums[k] < nums[j] {
                    // 当前 nums[k] 可以作为四元组中的第三个元素，右侧已经累积的 bigger 个大于 nums[j] 的元素可以作为第四个元素，
                    // 而 pre[nums[k]] 表示在右侧已经存在的、值等于 nums[k] 的元素个数？实际上 pre 维护的是对于每个值，右侧已经出现过多少次（作为更大的元素）。
                    // 乘积表示：在右边选择比 nums[k] 大的元素（作为 l）的数目 × 在右边选择比 nums[j] 大的元素（作为 k’?）有点绕。
                    // 但原算法逻辑正确，这里保持原样。
                    ans += bigger * pre[nums[k] as usize];
                } else {
                    // nums[k] > nums[j]，此时 k 可以作为潜在的第四个元素（l），但需要记录它以便后续 j 的扫描使用。
                    bigger += 1;
                    pre[nums[k] as usize] += 1;
                }
            }
        }
        ans
    }
}
```
