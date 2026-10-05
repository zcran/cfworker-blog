---
title: "leetcode-贪心183"
date: 2026-10-03T12:55:46+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 字典序最大的答案数组

给你一个长度为 n 的整数数组 nums。你可以重新排列其中的元素以形成任意 排列 perm。

定义一个长度为 15 的数组 power。对于每个 0 <= i < 15，考查 perm 的前 j 个元素的第 (14 - i) 位，power[i] 是满足这些位全为 1 的最大整数 j（其中 0 <= j <= n）。

二进制位的位置从右向左编号，从第 0 位开始。

返回可能得到的 字典序最大 的 power 数组。

排列 是数组中所有元素的一种重新排列。

置位 指的是数字在二进制表示中对应位的值为 1。

对于两个长度相同的数组，如果在它们不同的第一个下标处，数组 a 包含的元素大于数组 b 中的元素，则称数组 a 的 字典序大于 数组 b。


```
impl Solution {
    pub fn largest_power(mut nums: Vec<i32>) -> Vec<i32> {
        // 目标：字典序最大的 power 数组。
        // power[i] 对应第 (14 - i) 位，即该位在 perm 前缀中"全为 1"的最长长度。
        // 想让 power 字典序最大，等价于依次最大化第 14, 13, ..., 0 位的连续 1 前缀长度。
        // 用递归分区：在 [left, right) 内按当前位分区，1 放前 0 放后。
        //
        // 关键剪枝：mask 表示"当前位置仍有机会取到 1 的位集合"。
        //   只有在前面的元素于某位上都为 1，后续区间才能继续在该位上延伸。
        fn dfs(pos: i32, left: usize, right: usize, nums: &mut Vec<i32>, mask: i32) {
            if right - left <= 1 || pos < 0 || mask == 0 {
                return;
            }
            // 在 mask 中找 <= pos 的最高位
            let mut b = pos;
            while b >= 0 && (mask & (1 << b)) == 0 {
                b -= 1;
            }
            if b < 0 {
                return;
            }

            // 按第 b 位分区：为 1 的放前 [left, mid)，为 0 的放后 [mid, right)
            let mut mid = left;
            for i in left..right {
                if (nums[i] >> b) & 1 == 1 {
                    nums.swap(i, mid);
                    mid += 1;
                }
            }

            // 1 枝：此位全 1，去掉该位继续递归
            let new_mask = mask & !(1 << b);
            dfs(b - 1, left, mid, nums, new_mask);

            // 0 枝：只有 1 枝中所有元素都为 1 的位，才有机会延伸到 0 枝
            let mut and_ones = !0i32;
            for i in left..mid {
                and_ones &= nums[i];
            }
            dfs(b - 1, mid, right, nums, new_mask & and_ones);
        }

        let n = nums.len();
        dfs(14, 0, n, &mut nums, (1 << 15) - 1);

        // 计算 power：power[i] = perm 前缀中第 (14 - i) 位连续为 1 的最大长度
        let mut power = vec![0i32; 15];
        for i in 0..15 {
            let bit = 14 - i;
            let mut j = 0;
            while j < n && ((nums[j] >> bit) & 1) == 1 {
                j += 1;
            }
            power[i] = j as i32;
        }
        power
    }
}
```
