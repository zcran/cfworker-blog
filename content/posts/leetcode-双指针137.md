---
title: "leetcode-双指针137"
date: 2026-09-05T09:53:09+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 早餐组合

小扣在秋日市集选择了一家早餐摊位，一维整型数组 staple 中记录了每种主食的价格，一维整型数组 drinks 中记录了每种饮料的价格。小扣的计划选择一份主食和一款饮料，且花费不超过 x 元。请返回小扣共有多少种购买方案。

注意：答案需要以 1e9 + 7 (1000000007) 为底取模，如：计算初始结果为：1000000008，请返回 1


```
impl Solution {
    pub fn breakfast_number(staple: Vec<i32>, drinks: Vec<i32>, x: i32) -> i32 {
        const MOD: i64 = 1_000_000_007;
        let x = x as usize;

        // cnt[r]：剩余预算为 r 时可选的主食数量
        // 对每个主食价格 p，令剩余预算 r = x - p，则 cnt[r] += 1
        let mut cnt = vec![0i64; x + 1];
        for &p in &staple {
            let p = p as usize;
            if p <= x {
                cnt[x - p] += 1;
            }
        }

        // 后缀和：cnt[r] 表示剩余预算 >= r 的主食数量，
        // 即所有 staple <= x - r 的主食数
        for i in (0..x).rev() {
            cnt[i] += cnt[i + 1];
        }

        // 枚举饮料 d，剩余预算为 d 时，可搭配的主食数为 cnt[d]
        let mut ans = 0i64;
        for &d in &drinks {
            let d = d as usize;
            if d <= x {
                ans += cnt[d];
            }
        }

        (ans % MOD) as i32
    }
}
```
