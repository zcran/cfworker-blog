---
title: "leetcode-排序54"
date: 2026-10-05T00:35:03+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 数组中不等三元组的数目

给你一个下标从 0 开始的正整数数组 nums 。请你找出并统计满足下述条件的三元组 (i, j, k) 的数目：

· 0 <= i < j < k < nums.length
· nums[i]、nums[j] 和 nums[k] 两两不同 。
    · 换句话说：nums[i] != nums[j]、nums[i] != nums[k] 且 nums[j] != nums[k] 。

返回满足上述条件三元组的数目。


```
impl Solution {
    /// 统计满足 nums[i]、nums[j]、nums[k] 两两不同的三元组数目。
    ///
    /// 思路（一遍扫描）：
    /// 遍历到位置 i 作为第三个元素时：
    /// - pairs 表示前 i 个元素中已形成的二元组 (j, k) 数量（即 C(i, 2)）。
    /// - 其中与前缀中与 nums[i] 相等的元素相关的二元组数量为
    ///   cnt[x] * (i - cnt[x])，需要从 pairs 中剔除。
    /// - 因此以 nums[i] 结尾的合法三元组数为 pairs - cnt[x] * (i - cnt[x])。
    pub fn unequal_triplets(nums: Vec<i32>) -> i32 {
        let mut cnt = vec![0i32; 1001];
        let mut pairs = 0i32; // 当前前缀中二元组的数量
        let mut trips = 0i32;

        for (i, &x) in nums.iter().enumerate() {
            let c = cnt[x as usize];
            // 以 x 结尾、且三个元素互不相同的三元组数
            trips += pairs - c * (i as i32 - c);
            // 更新二元组数量：新加入的 x 可与前 i 个元素组成 i 个二元组
            pairs += i as i32 - c;
            cnt[x as usize] += 1;
        }

        trips
    }
}
```
