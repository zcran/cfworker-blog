---
title: "leetcode-计数78"
date: 2026-08-01T10:40:55+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 让数组不相等的最小总代价

给你两个下标从 0 开始的整数数组 nums1 和 nums2 ，两者长度都为 n 。

每次操作中，你可以选择交换 nums1 中任意两个下标处的值。操作的 开销 为两个下标的 和 。

你的目标是对于所有的 0 <= i <= n - 1 ，都满足 nums1[i] != nums2[i] ，你可以进行 任意次 操作，请你返回达到这个目标的 最小 总代价。

请你返回让 nums1 和 nums2 满足上述条件的 最小总代价 ，如果无法达成目标，返回 -1 。


```
impl Solution {
    pub fn minimum_total_cost(nums1: Vec<i32>, nums2: Vec<i32>) -> i64 {
        let n = nums1.len();
        let mut ans = 0i64;
        let mut swap_cnt = 0;
        let mut mode_cnt = 0;
        let mut mode = 0;
        let mut cnt = vec![0; n + 1];

        // 第一轮：处理所有 nums1[i] == nums2[i] 的位置
        // 这些位置必须通过交换来改变
        for i in 0..n {
            if nums1[i] == nums2[i] {
                ans += i as i64;
                swap_cnt += 1;
                let x = nums1[i] as usize;
                cnt[x] += 1;
                if cnt[x] > mode_cnt {
                    mode_cnt = cnt[x];
                    mode = x;
                }
            }
        }

        // 第二轮：如果冲突数量不足以消除众数的影响
        // 需要从其他位置借来可交换的元素
        for i in 0..n {
            if mode_cnt * 2 <= swap_cnt {
                break;
            }
            let x = nums1[i];
            let y = nums2[i];
            // 选择 nums1[i] != nums2[i] 且两边都不等于众数的位置
            if x != y && x as usize != mode && y as usize != mode {
                ans += i as i64;
                swap_cnt += 1;
            }
        }

        // 如果众数出现次数超过总交换次数的一半，无法完成
        if mode_cnt * 2 > swap_cnt {
            return -1;
        }
        ans
    }
}
```
