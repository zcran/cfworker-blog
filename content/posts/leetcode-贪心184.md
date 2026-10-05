---
title: "leetcode-贪心184"
date: 2026-10-03T12:55:46+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 蓄水

给定 N 个无限容量且初始均空的水缸，每个水缸配有一个水桶用来打水，第 i 个水缸配备的水桶容量记作 bucket[i]。小扣有以下两种操作：

升级水桶：选择任意一个水桶，使其容量增加为 bucket[i]+1
蓄水：将全部水桶接满水，倒入各自对应的水缸

每个水缸对应最低蓄水量记作 vat[i]，返回小扣至少需要多少次操作可以完成所有水缸蓄水要求。

注意：实际蓄水量 达到或超过 最低蓄水量，即完成蓄水要求。


```
impl Solution {
    pub fn store_water(bucket: Vec<i32>, vat: Vec<i32>) -> i32 {
        // 关键：决定总操作数的核心是"每个水桶要倒多少次水"，即统一的蓄水轮数 k。
        // 对固定的 k：
        //   第 i 个水桶需容量 >= ceil(vat[i] / k)，若不足则升级 bucket[i] 到该值，
        //   升级次数 = max(0, ceil(vat[i]/k) - bucket[i])。
        //   总操作数 = k（每轮蓄水一次）+ 升级次数之和。
        // k 越大，升级次数越少但蓄水轮数越多，故在合理范围内枚举 k 取最小值。
        let max_vat = match vat.iter().max() {
            Some(&0) => return 0, // 所有水缸都不需要蓄水
            Some(&m) => m,
            None => return 0,
        };

        let mut best = i32::MAX;

        // k 不必超过 max_vat（超过后每轮倒水次数只会更多，无意义）
        for k in 1..=max_vat {
            let mut ops = k; // 蓄水轮数
            for i in 0..vat.len() {
                let need = (vat[i] + k - 1) / k; // 该桶至少需要的容量
                ops += (need - bucket[i]).max(0);
            }
            best = best.min(ops);
            if best <= k {
                break; // 之后的 k 只会更大，不可能更优
            }
        }

        best
    }
}
```
