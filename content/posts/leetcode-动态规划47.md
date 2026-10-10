---
title: "leetcode-动态规划47"
date: 2026-10-05T20:39:57+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 使序列递增的最小交换次数

我们有两个长度相等且不为空的整型数组 nums1 和 nums2 。在一次操作中，我们可以交换 nums1[i] 和 nums2[i]的元素。

例如，如果 nums1 = [1,2,3,8] ， nums2 =[5,6,7,4] ，你可以交换 i = 3 处的元素，得到 nums1 =[1,2,3,4] 和 nums2 =[5,6,7,8] 。

返回 使 nums1 和 nums2 严格递增 所需操作的最小次数 。

数组 arr 严格递增 且  arr[0] < arr[1] < arr[2] < ... < arr[arr.length - 1] 。

注意：

用例保证可以实现操作。


```
impl Solution {
    /// 801. 使序列递增的最小交换次数
    ///
    /// 经典 DP：设 dp0[i] 表示第 i 位不交换、dp1[i] 表示第 i 位交换时，
    /// 前 i 位严格递增的最小交换次数。
    ///
    /// 转移条件（记 a0 = nums1[i-1], b0 = nums2[i-1], a1 = nums1[i], b1 = nums2[i]）：
    ///   - 若 a1 > a0 && b1 > b0：可以不交换（保持原样）
    ///   - 若 a1 > b0 && b1 > a0：可以交换（交叉后仍递增）
    ///
    /// 用滚动变量把空间压到 O(1)，一次遍历即可。
    pub fn min_swap(nums1: Vec<i32>, nums2: Vec<i32>) -> i32 {
        let n = nums1.len();
        // dp0 / dp1 分别对应"当前位不交换 / 交换"的最小次数
        let (mut dp0, mut dp1) = (0, 1);

        for i in 1..n {
            let (a0, b0) = (nums1[i - 1], nums2[i - 1]);
            let (a1, b1) = (nums1[i], nums2[i]);

            // 分别计算本位的两种候选值
            let (mut ndp0, mut ndp1) = (i32::MAX, i32::MAX);

            // 情况一：原序本身递增 → 可不交换
            if a1 > a0 && b1 > b0 {
                ndp0 = ndp0.min(dp0);
                ndp1 = ndp1.min(dp1 + 1);
            }
            // 情况二：交叉后递增 → 可交换
            if a1 > b0 && b1 > a0 {
                ndp0 = ndp0.min(dp1);
                ndp1 = ndp1.min(dp0 + 1);
            }

            dp0 = ndp0;
            dp1 = ndp1;
        }

        dp0.min(dp1)
    }
}
```
