---
title: "leetcode-位掩码25"
date: 2026-05-18T10:27:59+08:00
tags: ["leetcode", "位掩码"]
draft: false
---


## 数组的最大与和


给你一个长度为 n 的整数数组 nums 和一个整数 numSlots ，满足2 * numSlots >= n 。总共有 numSlots 个篮子，编号为 1 到 numSlots 。

你需要把所有 n 个整数分到这些篮子中，且每个篮子 至多 有 2 个整数。一种分配方案的 与和 定义为每个数与它所在篮子编号的 按位与运算 结果之和。

比方说，将数字 [1, 3] 放入篮子 1 中，[4, 6] 放入篮子 2 中，这个方案的与和为 (1 AND 1) + (3 AND 1) + (4 AND 2) + (6 AND 2) = 1 + 1 + 0 + 2 = 4 。

请你返回将 nums 中所有数放入 numSlots 个篮子中的最大与和。


```
impl Solution {
    /// 计算将 nums 中的数字分配到 num_slots 个槽位（每个槽位最多 2 个数字）
    /// 所能得到的最大按位与和（每个数字与槽位编号进行 AND 后求和）
    pub fn maximum_and_sum(nums: Vec<i32>, num_slots: i32) -> i32 {
        let n = nums.len();
        let slots = num_slots as usize;
        // 三进制状态总数：每个槽位有 0,1,2 三种状态
        let total_states = 3_usize.pow(slots as u32);
        let mut dp = vec![0; total_states];

        // 遍历所有状态（mask 是三进制数，每位表示对应槽位已放数字个数）
        for mask in 1..total_states {
            // 1. 计算 mask 中已放置的数字个数 cnt
            let mut cnt = 0;
            let mut m = mask;
            for _ in 0..slots {
                cnt += m % 3;
                m /= 3;
            }
            if cnt > n {
                continue; // 数字不够，无法到达此状态
            }

            // 2. 尝试将第 cnt-1 个数字放入某个有空位的槽位
            let current_num = nums[cnt - 1];
            let mut m = mask;
            let mut weight = 1; // 三进制当前位的权值：3^i
            for slot in 0..slots {
                let used = m % 3;
                if used > 0 {
                    let prev_mask = mask - weight; // 移除当前槽位的一个数字
                    let gain = current_num & ((slot + 1) as i32);
                    dp[mask] = dp[mask].max(dp[prev_mask] + gain);
                }
                m /= 3;
                weight *= 3;
            }
        }

        *dp.iter().max().unwrap()
    }
}
```
