---
title: "leetcode-贪心167"
date: 2026-10-03T12:55:45+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 找到带限制序列的最大值

给你一个整数 n，一个二维整数数组 restrictions，以及一个长度为 n - 1 的整数数组 diff。你的任务是构造一个长度为 n 的序列，记为 a[0], a[1], ..., a[n - 1]，使其满足以下条件：

· a[0] 为 0。
· 序列中的所有元素都是 非负整数 。
· 对于每个下标 i (0 <= i <= n - 2)，满足 abs(a[i] - a[i + 1]) <= diff[i]。
· 对于每个 restrictions[i] = [idx, maxVal]，序列中位置 idx 的值不得超过 maxVal（即 a[idx] <= maxVal）。

你的目标是在满足上述所有条件的情况下，构造一个合法的序列并 最大化 该序列中的 最大 值。

返回一个整数，表示最优序列中出现的 最大 值。




```
impl Solution {
    pub fn find_max_val(n: i32, restrictions: Vec<Vec<i32>>, diff: Vec<i32>) -> i32 {
        // a[0] = 0，每个位置有上限 max_val（来自 restrictions）。
        // 先从前往后传播：a[i+1] = min(a[i] + diff[i], max_val[i+1])
        // 再从后往前修正：a[i] = min(a[i], a[i+1] + diff[i])
        // 经过两次传播，得到每个位置的可行最大值，取其中最大者。
        let n = n as usize;
        let mut max_val = vec![i32::MAX; n];
        for r in &restrictions {
            max_val[r[0] as usize] = r[1];
        }
        max_val[0] = 0;

        let mut a = vec![0i32; n];
        a[0] = 0;
        for i in 0..n - 1 {
            a[i + 1] = (a[i] + diff[i]).min(max_val[i + 1]);
        }
        for i in (1..n - 1).rev() {
            a[i] = a[i].min(a[i + 1] + diff[i]);
        }

        *a.iter().max().unwrap()
    }
}
```
