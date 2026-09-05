---
title: "leetcode-前缀和107"
date: 2026-08-25T10:02:52+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


##  零数组变换 II

给你一个长度为 n 的整数数组 nums 和一个二维数组 queries，其中 queries[i] = [li, ri, vali]。

每个 queries[i] 表示在 nums 上执行以下操作：

将 nums 中 [li, ri] 范围内的每个下标对应元素的值 最多 减少 vali。

每个下标的减少的数值可以独立选择。

零数组 是指所有元素都等于 0 的数组。

返回 k 可以取到的 最小非负 值，使得在 顺序 处理前 k 个查询后，nums 变成 零数组。如果不存在这样的 k，则返回 -1。


```
impl Solution {
    pub fn min_zero_array(nums: Vec<i32>, queries: Vec<Vec<i32>>) -> i32 {
        let n = nums.len();
        let q = queries.len();

        // 检查所有查询是否能满足
        if !Self::can_make_zero(&nums, &queries, q) {
            return -1;
        }

        // 二分查找最小 k
        let (mut lo, mut hi) = (0, q);
        while lo < hi {
            let mid = (lo + hi) / 2;
            if Self::can_make_zero(&nums, &queries, mid) {
                hi = mid;
            } else {
                lo = mid + 1;
            }
        }
        lo as i32
    }

    fn can_make_zero(nums: &[i32], queries: &[Vec<i32>], k: usize) -> bool {
        let n = nums.len();
        let mut diff = vec![0; n + 1];

        // 构建差分数组，只使用前 k 个查询
        for i in 0..k {
            let l = queries[i][0] as usize;
            let r = queries[i][1] as usize;
            let v = queries[i][2];
            diff[l] += v;
            diff[r + 1] -= v;
        }

        // 前缀和检查每个位置是否足够
        let mut ops = 0;
        for i in 0..n {
            ops += diff[i];
            if ops < nums[i] {
                return false;
            }
        }
        true
    }
}
```
