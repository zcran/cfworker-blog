---
title: "leetcode-枚举47"
date: 2026-07-09T10:05:01+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 让所有学生保持开心的分组方法数

给你一个下标从 0 开始、长度为 n 的整数数组 nums ，其中 n 是班级中学生的总数。班主任希望能够在让所有学生保持开心的情况下选出一组学生：

如果能够满足下述两个条件之一，则认为第 i 位学生将会保持开心：

这位学生被选中，并且被选中的学生人数 严格大于 nums[i] 。
这位学生没有被选中，并且被选中的学生人数 严格小于 nums[i] 。

返回能够满足让所有学生保持开心的分组方法的数目。


```
impl Solution {
    pub fn count_ways(mut nums: Vec<i32>) -> i32 {
        nums.sort_unstable(); // 使用不稳定排序，性能略优
        let n = nums.len();
        let mut res = 0;

        // k 表示被选中的学生人数，范围 0..=n
        // 对于选中的 k 人，需要满足：
        // 1. 被选中的 k 人：其 nums[i] 必须 < k（严格小于）
        // 2. 未被选中的 n-k 人：其 nums[i] 必须 > k（严格大于）
        //
        // 排序后，前 k 个（最小的 k 个）应该是被选中的，后 n-k 个是未被选中的
        // 因此需要满足：前 k 个最大值 < k 且 后 n-k 个最小值 > k

        for k in 0..=n {
            // 检查前 k 个元素：nums[k-1] 是前 k 个中的最大值，必须 < k
            if k > 0 && nums[k - 1] >= k as i32 {
                continue;
            }
            // 检查后 n-k 个元素：nums[k] 是后 n-k 个中的最小值，必须 > k
            if k < n && nums[k] <= k as i32 {
                continue;
            }
            res += 1;
        }

        res
    }
}
```
