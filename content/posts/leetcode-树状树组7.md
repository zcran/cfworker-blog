---
title: "leetcode-树状树组7"
date: 2026-05-03T20:07:52+08:00
tags: ["leetcode", "树状树组"]
draft: false
---


## 最长递增子序列的个数

给定一个未排序的整数数组 nums ， 返回最长递增子序列的个数 。

注意 这个数列必须是 严格 递增的。



示例 1:

输入: [1,3,5,4,7]
输出: 2
解释: 有两个最长递增子序列，分别是 [1, 3, 4, 7] 和[1, 3, 5, 7]。
示例 2:

输入: [2,2,2,2,2]
输出: 5
解释: 最长递增子序列的长度是1，并且存在5个子序列的长度为1，因此输出5。


```
impl Solution {
    /// 计算最长递增子序列的个数
    /// 动态规划：dp[i] 表示以 nums[i] 结尾的 LIS 长度，cnt[i] 表示该长度的 LIS 个数
    /// 时间复杂度 O(n²)，空间复杂度 O(n)
    pub fn find_number_of_lis(nums: Vec<i32>) -> i32 {
        let n = nums.len();
        // 使用 fold 函数式累积 dp 和 cnt 数组
        let (dp, cnt) = nums.iter().enumerate().fold(
            (vec![1; n], vec![1; n]), // 初始时每个元素自身构成一个长度为 1 的 LIS
            |(mut dp, mut cnt), (i, &x)| {
                // 遍历 i 之前的所有元素
                for j in 0..i {
                    if nums[j] < x {
                        if dp[j] + 1 > dp[i] {
                            dp[i] = dp[j] + 1;
                            cnt[i] = cnt[j];
                        } else if dp[j] + 1 == dp[i] {
                            cnt[i] += cnt[j];
                        }
                    }
                }
                (dp, cnt)
            },
        );
        // 找出 LIS 的最大长度
        let max_len = *dp.iter().max().unwrap();
        // 累加所有等于最大长度的 LIS 个数
        (0..n).filter(|&i| dp[i] == max_len).map(|i| cnt[i]).sum()
    }
}
```
