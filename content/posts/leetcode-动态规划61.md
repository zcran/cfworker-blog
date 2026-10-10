---
title: "leetcode-动态规划61"
date: 2026-10-05T20:39:58+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


##  不相交的线

在两条独立的水平线上按给定的顺序写下 nums1 和 nums2 中的整数。

现在，可以绘制一些连接两个数字 nums1[i] 和 nums2[j] 的直线，这些直线需要同时满足：

 nums1[i] == nums2[j]

且绘制的直线不与任何其他连线（非水平线）相交。

请注意，连线即使在端点也不能相交：每个数字只能属于一条连线。

以这种方法绘制线条，并返回可以绘制的最大连线数。


```
impl Solution {
    /// 不相交的线
    ///
    /// 问题等价于求 nums1 和 nums2 的最长公共子序列（LCS）长度：
    /// 不相交的连线对应两序列中下标严格递增的相同元素配对。
    ///
    /// 标准 LCS 递推：
    ///   nums1[i-1] == nums2[j-1] → dp[i][j] = dp[i-1][j-1] + 1
    ///   否则                     → dp[i][j] = max(dp[i-1][j], dp[i][j-1])
    ///
    /// 用一维滚动数组：逆序更新 j，使 dp[j-1] 仍代表上一行，空间从 O(m*n) 降到 O(n)。
    pub fn max_uncrossed_lines(nums1: Vec<i32>, nums2: Vec<i32>) -> i32 {
        let n = nums2.len();
        let mut dp = vec![0i32; n + 1];

        for &a in &nums1 {
            let mut prev = 0; // 保存 dp[j-1] 在上一行的值（对角线）
            for j in 1..=n {
                let temp = dp[j]; // 记录当前 dp[j]，作为下一轮的"上一行对角线"
                dp[j] = if a == nums2[j - 1] {
                    prev + 1
                } else {
                    dp[j].max(dp[j - 1])
                };
                prev = temp;
            }
        }

        dp[n]
    }
}
```
