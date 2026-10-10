---
title: "leetcode-动态规划143"
date: 2026-10-05T20:40:03+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 统计数组中的美丽分割

给你一个整数数组 nums 。

如果数组 nums 的一个分割满足以下条件，我们称它是一个 美丽 分割：

数组 nums 分为三段 非空子数组：nums1 ，nums2 和 nums3 ，三个数组 nums1 ，nums2 和 nums3 按顺序连接可以得到 nums 。

子数组 nums1 是子数组 nums2 的 前缀 或者 nums2 是 nums3 的 前缀。
请你返回满足以上条件的分割 数目 。

子数组 指的是一个数组里一段连续 非空 的元素。

前缀 指的是一个数组从头开始到中间某个元素结束的子数组。


```
impl Solution {
    pub fn beautiful_splits(nums: Vec<i32>) -> i32 {
        let n = nums.len();
        if n < 3 {
            return 0;
        }

        // Z 函数：z[i] 表示 s[start..] 与其后缀 s[start+i..] 的最长公共前缀长度
        let calc_z = |start: usize| -> Vec<usize> {
            let len = n - start;
            let mut z = vec![0usize; len];
            let (mut l, mut r) = (0usize, 0usize); // z-box 区间 [l, r]

            for i in 1..len {
                if i <= r {
                    z[i] = z[i - l].min(r - i + 1);
                }
                while i + z[i] < len && nums[start + z[i]] == nums[start + i + z[i]] {
                    l = i;
                    r = i + z[i];
                    z[i] += 1;
                }
            }
            z
        };

        let z0 = calc_z(0);
        let mut ans = 0;

        // 枚举第二个子数组的起点 i（也是第一段的长度）
        for i in 1..n - 1 {
            let z = calc_z(i);

            // 枚举第三段的起点 j（也是第二段的结束位置）
            for j in i + 1..n {
                // 情况1：nums1 是 nums2 的前缀，需 i <= j - i 且 z0[i] >= i
                // 情况2：nums2 是 nums3 的前缀，需 z[j - i] >= j - i
                if (i <= j - i && z0[i] >= i) || z[j - i] >= j - i {
                    ans += 1;
                }
            }
        }

        ans
    }
}
```
