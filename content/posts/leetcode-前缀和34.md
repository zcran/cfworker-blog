---
title: "leetcode-前缀和34"
date: 2026-08-25T10:02:48+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 使数组互补的最少操作次数

给你一个长度为 偶数 n 的整数数组 nums 和一个整数 limit 。每一次操作，你可以将 nums 中的任何整数替换为 1 到 limit 之间的另一个整数。

如果对于所有下标 i（下标从 0 开始），nums[i] + nums[n - 1 - i] 都等于同一个数，则数组 nums 是 互补的 。例如，数组 [1,2,3,4] 是互补的，因为对于所有下标 i ，nums[i] + nums[n - 1 - i] = 5 。

返回使数组 互补 的 最少 操作次数。


```
impl Solution {
    /// 计算使数组互补的最少操作次数
    ///
    /// 互补数组：所有 nums[i] + nums[n-1-i] 相等
    ///
    /// 思路：差分数组统计每个目标和所需的操作次数
    /// 对于每对 (a,b)，其中 a <= b：
    /// - 0次操作：和为 a+b（固定）
    /// - 1次操作：和的范围为 [a+1, b+limit]（改变一个数）
    /// - 2次操作：其他所有和（改变两个数）
    pub fn min_moves(nums: Vec<i32>, limit: i32) -> i32 {
        let n = nums.len();
        let max_sum = 2 * limit + 1; // 可能的最大和
        let mut diff = vec![0; (max_sum + 2) as usize];

        for i in 0..n / 2 {
            let a = nums[i].min(nums[n - 1 - i]);
            let b = nums[i].max(nums[n - 1 - i]);

            // 差分数组标记不同操作次数对应的和区间
            // 初始所有和需要 2 次操作
            diff[2] += 2;

            // 1 次操作可以得到的和：[a+1, b+limit]
            // 注：a+b 本身需要 0 次操作，特殊处理
            diff[(a + 1) as usize] -= 1;
            diff[(a + b) as usize] -= 1; // a+b 从 1 次中排除
            diff[(a + b + 1) as usize] += 1;
            diff[(b + limit + 1) as usize] += 1;
        }

        // 前缀和求每个目标和的最终操作次数
        let mut min_ops = n as i32;
        let mut cur_ops = 0;

        for sum in 2..=max_sum as usize {
            cur_ops += diff[sum];
            min_ops = min_ops.min(cur_ops);
        }

        min_ops
    }
}
```
