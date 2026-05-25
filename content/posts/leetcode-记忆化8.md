---
title: "leetcode-记忆化8"
date: 2026-05-06T20:22:33+08:00
tags: ["leetcode", "记忆化"]
draft: false
---



## 移除盒子


给出一些不同颜色的盒子 boxes ，盒子的颜色由不同的正数表示。

你将经过若干轮操作去去掉盒子，直到所有的盒子都去掉为止。每一轮你可以移除具有相同颜色的连续 k 个盒子（k >= 1），这样一轮之后你将得到 k * k 个积分。

返回 你能获得的最大积分和 。


```
impl Solution {
    /// 移除盒子问题 (LeetCode 546)
    pub fn remove_boxes(boxes: Vec<i32>) -> i32 {
        let n = boxes.len();
        // dp[l][r][k] 第三维使用 n+1 是因为 k 最大可能为 n
        let mut dp = vec![vec![vec![0; n + 1]; n]; n];
        Self::calculate_points(&boxes, 0, (n - 1) as i32, 0, &mut dp)
    }

    /// 递归计算区间 [l, r] 且右侧已有 k 个与 boxes[r] 同色的盒子时的最大得分。
    /// k 使用 i32 类型，避免与 i32 得分相加时的类型转换。
    fn calculate_points(
        boxes: &[i32],
        l: i32,
        r: i32,
        k: i32,
        dp: &mut Vec<Vec<Vec<i32>>>,
    ) -> i32 {
        if l > r {
            return 0;
        }
        let l_usize = l as usize;
        let r_usize = r as usize;
        let k_usize = k as usize;

        if dp[l_usize][r_usize][k_usize] != 0 {
            return dp[l_usize][r_usize][k_usize];
        }

        // 方案1：直接移除 boxes[r] 及其右侧 k 个同色盒子
        let mut res = Self::calculate_points(boxes, l, r - 1, 0, dp) + (k + 1) * (k + 1);

        // 方案2：在 [l, r-1] 中寻找可以合并的位置 i
        for i in l..r {
            let i_usize = i as usize;
            if boxes[i_usize] == boxes[r_usize] {
                let val = Self::calculate_points(boxes, l, i, k + 1, dp)
                    + Self::calculate_points(boxes, i + 1, r - 1, 0, dp);
                if val > res {
                    res = val;
                }
            }
        }

        dp[l_usize][r_usize][k_usize] = res;
        res
    }
}
```
