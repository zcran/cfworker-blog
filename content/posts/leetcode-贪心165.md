---
title: "leetcode-贪心165"
date: 2026-10-03T12:55:45+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 使循环数组余额非负的最少移动次数

给你一个长度为 n 的 环形 数组 balance，其中 balance[i] 是第 i 个人的净余额。

在一次移动中，一个人可以将 正好 1 个单位的余额转移给他的左邻居或右邻居。

返回使每个人都拥有 非负 余额所需的 最小 移动次数。如果无法实现，则返回 -1。

注意：输入保证初始时 至多 有一个下标具有 负 余额。


```
impl Solution {
    pub fn min_moves(balance: Vec<i32>) -> i64 {
        // 输入保证至多一个负数下标。
        let n = balance.len();
        let mut total = 0i64;
        let mut neg_idx = -1i32;

        for (i, &x) in balance.iter().enumerate() {
            total += x as i64;
            if x < 0 {
                neg_idx = i as i32;
            }
        }

        // 总余额为负，无法让所有人非负
        if total < 0 {
            return -1;
        }
        // 没有负数，无需操作
        if neg_idx < 0 {
            return 0;
        }

        // 把负余额填平：从负下标向两侧由近到远取余额，
        // 每个单位的移动代价为其到负下标的距离。
        let neg_idx = neg_idx as usize;
        let mut need = -(balance[neg_idx] as i64); // 还需补足的余额
        let mut ans = 0i64;

        let mut dis = 1usize;
        while need > 0 {
            let left = balance[(neg_idx + n - dis) % n] as i64;
            let right = balance[(neg_idx + dis) % n] as i64;
            let s = left + right;

            if s >= need {
                ans += need * dis as i64;
                return ans;
            }
            ans += s * dis as i64;
            need -= s;
            dis += 1;
        }

        ans
    }
}
```
