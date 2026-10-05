---
title: "leetcode-二分查找41"
date: 2026-09-28T12:29:12+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 得到山形数组的最少删除次数

我们定义 arr 是 山形数组 当且仅当它满足：

· arr.length >= 3
· 存在某个下标 i （从 0 开始） 满足 0 < i < arr.length - 1 且：
    · arr[0] < arr[1] < ... < arr[i - 1] < arr[i]
    · arr[i] > arr[i + 1] > ... > arr[arr.length - 1]

给你整数数组 nums​ ，请你返回将 nums 变成 山形状数组 的​ 最少 删除次数。


```
impl Solution {
    pub fn minimum_mountain_removals(nums: Vec<i32>) -> i32 {
        let n = nums.len();
        let mut suf = vec![0; n]; // suf[i]: 以 nums[i] 为起点的最长严格递减子序列长度

        // 从右往左计算 suf，使用 tails 维护当前最长递减子序列的“贪心最小末尾”
        let mut tails = Vec::new();
        for (i, &x) in nums.iter().enumerate().rev() {
            let j = tails.partition_point(|&v| v < x);
            if j == tails.len() {
                tails.push(x);
            } else {
                tails[j] = x;
            }
            suf[i] = j + 1;
        }

        let mut max_len = 0;
        tails.clear();

        // 从左往右计算以 nums[i] 结尾的最长严格递增子序列长度，并合并 suf 得到山形长度
        for (i, &x) in nums.iter().enumerate() {
            let j = tails.partition_point(|&v| v < x);
            if j == tails.len() {
                tails.push(x);
            } else {
                tails[j] = x;
            }

            let pre = j + 1; // 以 nums[i] 结尾的最长严格递增子序列长度
            if pre >= 2 && suf[i] >= 2 {
                max_len = max_len.max(pre + suf[i] - 1); // nums[i] 被重复计算一次
            }
        }

        (n - max_len) as i32
    }
}
```
