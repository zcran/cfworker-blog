---
title: "leetcode-二分查找66"
date: 2026-09-28T12:29:14+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 长度递增组的最大数目

给你一个下标从 0 开始、长度为 n 的数组 usageLimits 。

你的任务是使用从 0 到 n - 1 的数字创建若干组，并确保每个数字 i 在 所有组 中使用的次数总共不超过 usageLimits[i] 次。此外，还必须满足以下条件：

每个组必须由 不同 的数字组成，也就是说，单个组内不能存在重复的数字。
每个组（除了第一个）的长度必须 严格大于 前一个组。

在满足所有条件的情况下，以整数形式返回可以创建的最大组数。


```
impl Solution {
    pub fn max_increasing_groups(usage_limits: Vec<i32>) -> i32 {
        let n = usage_limits.len();
        // 排序后从小到大分配各组所需的出现次数，便于贪心判断
        let mut limits: Vec<i64> = usage_limits.into_iter().map(|x| x as i64).collect();
        limits.sort_unstable();

        // need 表示当前这一组需要每个数字至少出现的次数（组长度）
        let mut need = 1_i64;
        for i in 0..n {
            if limits[i] >= need {
                limits[i] -= need;
                need += 1;
            }
            // 把当前剩余的额度传给下一个位置，相当于合并可用次数
            if i + 1 < n {
                limits[i + 1] += limits[i];
            }
        }

        (need - 1) as i32
    }
}
```
