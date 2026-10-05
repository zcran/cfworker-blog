---
title: "leetcode-前缀和142"
date: 2026-08-25T10:02:55+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 回文子数组求和

给你一个整数数组 nums。

你的任务是找出 nums 中一个 回文子数组 的 最大 元素和。

返回这样的子数组的 最大 元素和。

子数组 是数组中一个连续的 非空 元素序列。

如果一个 子数组 正着读和反着读都相同，则称其为 回文 。


```
impl Solution {
    pub fn get_sum(nums: Vec<i32>) -> i64 {
        let n = nums.len();
        if n == 0 {
            return 0;
        }

        // ---------- 1. Manacher 预处理 ----------
        // 构造辅助数组 t，使所有回文都变成奇回文
        let mut t = Vec::with_capacity(n * 2 + 3);
        t.push(-2); // 左边界哨兵
        for &x in &nums {
            t.push(-1);
            t.push(x);
        }
        t.push(-1);
        t.push(-3); // 右边界哨兵

        let m = t.len();
        // half_len[i] 表示以 t[i] 为中心的最长回文半径（包含中心）
        let mut half_len = vec![1; m];
        let (mut box_m, mut box_r) = (0, 0); // 当前最右回文的中心和右边界（开区间）

        for i in 1..m - 1 {
            let mut hl = 1;
            if i < box_r {
                let mirror = box_m * 2 - i;
                hl = half_len[mirror].min(box_r - i);
            }

            // 暴力扩展
            while i >= hl && i + hl < m && t[i - hl] == t[i + hl] {
                hl += 1;
                box_m = i;
                box_r = i + hl;
            }
            half_len[i] = hl;
        }

        // ---------- 2. 前缀和 ----------
        let mut prefix = vec![0i64; n + 1];
        for i in 0..n {
            prefix[i + 1] = prefix[i] + nums[i] as i64;
        }

        // ---------- 3. 计算答案 ----------
        let mut ans = i64::MIN;

        for i in 1..m - 1 {
            let hl = half_len[i];

            if hl == 1 {
                // 长度为 1 的回文，只可能是 t[i] 本身
                // 当 i 为偶数时对应 nums 中的元素，奇数时是分隔符
                if i % 2 == 0 {
                    let idx = i / 2 - 1;
                    ans = ans.max(nums[idx] as i64);
                }
                continue;
            }

            // 有效回文区间在 t 上为 [i-hl+2, i+hl-2]（去掉两端的分隔符）
            // 映射到 nums 的下标范围：
            let left = (i - hl) / 2;
            let right = (i + hl) / 2 - 2;

            if left >= 0 && right < n && left <= right {
                let sum = prefix[right + 1] - prefix[left];
                ans = ans.max(sum);
            }
        }

        ans
    }
}
```
