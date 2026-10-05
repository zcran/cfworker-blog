---
title: "leetcode-贪心101"
date: 2026-10-03T12:55:40+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 划分数组使最大差为 K

给你一个整数数组 nums 和一个整数 k 。你可以将 nums 划分成一个或多个 子序列 ，使 nums 中的每个元素都 恰好 出现在一个子序列中。

在满足每个子序列中最大值和最小值之间的差值最多为 k 的前提下，返回需要划分的 最少 子序列数目。

子序列 本质是一个序列，可以通过删除另一个序列中的某些元素（或者不删除）但不改变剩下元素的顺序得到。


```
impl Solution {
    pub fn partition_array(mut nums: Vec<i32>, k: i32) -> i32 {
        // 排序后，同一组内元素必然连续；用当前组最小值 rec 判断新元素能否并入
        nums.sort_unstable();

        let mut groups = 1;
        let mut rec = nums[0];
        for &num in &nums {
            if num - rec > k {
                groups += 1;
                rec = num;
            }
        }
        groups
    }
}
```
