---
title: "leetcode-枚举49"
date: 2026-07-09T10:05:01+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 最大化数组末位元素的最少操作次数

给你两个下标从 0 开始的整数数组 nums1 和 nums2 ，这两个数组的长度都是 n 。

你可以执行一系列 操作（可能不执行）。

在每次操作中，你可以选择一个在范围 [0, n - 1] 内的下标 i ，并交换 nums1[i] 和 nums2[i] 的值。

你的任务是找到满足以下条件所需的 最小 操作次数：

nums1[n - 1] 等于 nums1 中所有元素的 最大值 ，即 nums1[n - 1] = max(nums1[0], nums1[1], ..., nums1[n - 1]) 。
nums2[n - 1] 等于 nums2 中所有元素的 最大值 ，即 nums2[n - 1] = max(nums2[0], nums2[1], ..., nums2[n - 1]) 。
以整数形式，表示并返回满足上述 全部 条件所需的 最小 操作次数，如果无法同时满足两个条件，则返回 -1 。


```
impl Solution {
    pub fn min_operations(nums1: Vec<i32>, nums2: Vec<i32>) -> i32 {
        let n = nums1.len();
        let last1 = nums1[n - 1];
        let last2 = nums2[n - 1];

        // 尝试两种方案：
        // 方案A：不交换最后一对，目标最大值是 (last1, last2)
        // 方案B：交换最后一对，目标最大值是 (last2, last1)
        let mut ans = i32::MAX;

        for (target1, target2, base_ops) in [(last1, last2, 0), (last2, last1, 1)] {
            let mut ops = base_ops;
            let mut possible = true;

            for i in 0..n - 1 {
                let x = nums1[i];
                let y = nums2[i];

                // 判断当前对是否需要交换以满足约束
                let keep_ok = x <= target1 && y <= target2;
                let swap_ok = x <= target2 && y <= target1;

                if keep_ok && swap_ok {
                    // 两种都可以，选择不交换（节省操作次数）
                    continue;
                } else if swap_ok {
                    // 必须交换
                    ops += 1;
                } else if keep_ok {
                    // 必须保持
                    continue;
                } else {
                    // 无论如何都无法满足
                    possible = false;
                    break;
                }
            }

            if possible {
                ans = ans.min(ops);
            }
        }

        if ans == i32::MAX { -1 } else { ans }
    }
}
```
