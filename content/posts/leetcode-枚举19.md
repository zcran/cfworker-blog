---
title: "leetcode-枚举19"
date: 2026-07-09T10:05:00+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 分割数组的最多方案数

给你一个下标从 0 开始且长度为 n 的整数数组 nums 。分割 数组 nums 的方案数定义为符合以下两个条件的 pivot 数目：

1 <= pivot < n

nums[0] + nums[1] + ... + nums[pivot - 1] == nums[pivot] + nums[pivot + 1] + ... + nums[n - 1]

同时给你一个整数 k 。你可以将 nums 中 一个 元素变为 k 或 不改变 数组。

请你返回在 至多 改变一个元素的前提下，最多 有多少种方法 分割 nums 使得上述两个条件都满足。


```
impl Solution {
    /// 返回至多改变一个元素为 k 后，数组的最大分割方案数。
    /// 分割点 pivot 满足：左半部分和 == 右半部分和。
    pub fn ways_to_partition(nums: Vec<i32>, k: i32) -> i32 {
        use std::collections::HashMap;

        let n = nums.len();
        let k = k as i64;
        let nums: Vec<i64> = nums.iter().map(|&x| x as i64).collect();

        // 前缀和数组，prefix[i] 表示 nums[0..i) 的和
        let mut prefix = vec![0; n + 1];
        for i in 0..n {
            prefix[i + 1] = prefix[i] + nums[i];
        }
        let total = prefix[n];

        // 记录每个前缀和（分割点左侧和）在分割点左右两侧的出现次数
        // left_count: 分割点左侧的前缀和（即 nums[0..pivot) 的和）
        // right_count: 分割点右侧的前缀和（即 nums[0..pivot] 的和）
        // 注意：right_count 初始不包含最右侧的完整和（因为 pivot < n）
        let mut left_count: HashMap<i64, i32> = HashMap::new();
        let mut right_count: HashMap<i64, i32> = HashMap::new();

        // 初始化右侧哈希表：记录每个前缀和作为分割点右侧和的次数
        // 对于分割点 pivot，右侧和为 prefix[pivot]
        for pivot in 1..n {
            *right_count.entry(prefix[pivot]).or_insert(0) += 1;
        }

        // 不修改任何元素时的最大分割数
        let mut max_ways = 0;
        if total % 2 == 0 {
            let target = total / 2;
            max_ways = *left_count.get(&target).unwrap_or(&0)
                     + *right_count.get(&target).unwrap_or(&0);
        }

        // 尝试修改每个元素 nums[i] -> k
        for i in 0..n {
            let delta = k - nums[i];
            let new_total = total + delta;

            // 修改 nums[i] 后，分割点 pivot 需要满足：
            // 左侧和 = (new_total) / 2
            // 当 pivot <= i 时，左侧和不包含修改的元素
            // 当 pivot > i 时，左侧和包含修改的元素
            if new_total % 2 == 0 {
                let target = new_total / 2;

                // 分割点在 i 左侧或等于 i：左侧和不受影响，仍为 prefix[pivot]
                let ways_left = *left_count.get(&target).unwrap_or(&0);

                // 分割点在 i 右侧：左侧和增加了 delta，所以原左侧和 = target - delta
                let target_right = target - delta;
                let ways_right = *right_count.get(&target_right).unwrap_or(&0);

                max_ways = max_ways.max(ways_left + ways_right);
            }

            // 移动指针 i：将 prefix[i+1] 从右侧移到左侧
            // 因为后续的 i 更大，当前分割点前缀和会进入左侧
            let key = prefix[i + 1];
            *left_count.entry(key).or_insert(0) += 1;
            *right_count.entry(key).or_insert(0) -= 1;
            if right_count.get(&key) == Some(&0) {
                right_count.remove(&key);
            }
        }

        max_ways
    }
}
```
