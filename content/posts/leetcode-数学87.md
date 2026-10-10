---
title: "leetcode-数学87"
date: 2026-10-07T20:39:34+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 构造奇偶一致的数组 II

给你一个长度为 n 的数组 nums1，其中包含 互不相同 的整数。

你需要构造另一个长度为 n 的数组 nums2，使得 nums2 中的元素要么全部为 奇数，要么全部为 偶数。

对于每个下标 i，你必须从以下两种选择中 任选其一（顺序不限）：

nums2[i] = nums1[i]
nums2[i] = nums1[i] - nums1[j]，其中 j != i，且满足 nums1[i] - nums1[j] >= 1

如果能够构造出满足条件的数组，则返回 true；否则，返回 false。




```
impl Solution {
    pub fn uniform_array(nums1: Vec<i32>) -> bool {
        // 设 mn 为最小值：
        //   - 若 mn 是奇数，则任意数都可减去 mn 得到相反奇偶性，
        //     因此所有数都能统一成奇数，返回 true。
        //   - 若 mn 是偶数，则减去任意数（它们奇偶性与 mn 不一定相同）后，
        //     只能把数变成与 mn 同奇偶（偶数）或保持原奇偶；
        //     只要存在奇数就无法统一（奇数减偶数仍为奇数，减不掉），返回 false。
        // 结论：mn 为奇数 → true；mn 为偶数 → 当且仅当没有奇数时 true。
        let mn = *nums1.iter().min().unwrap();
        let has_odd = nums1.iter().any(|&v| v % 2 != 0);

        mn % 2 != 0 || !has_odd
    }
}
```
