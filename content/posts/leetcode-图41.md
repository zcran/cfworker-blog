---
title: "leetcode-图41"
date: 2026-07-27T10:01:37+08:00
tags: ["leetcode", "图"]
draft: false
---


## 单位转换 I

有 n 种单位，编号从 0 到 n - 1。给你一个二维整数数组 conversions，长度为 n - 1，其中 conversions[i] = [sourceUniti, targetUniti, conversionFactori] ，表示一个 sourceUniti 类型的单位等于 conversionFactori 个 targetUniti 类型的单位。

请你返回一个长度为 n 的数组 baseUnitConversion，其中 baseUnitConversion[i] 表示 一个 0 类型单位等于多少个 i 类型单位。由于结果可能很大，请返回每个 baseUnitConversion[i] 对 10^9 + 7 取模后的值。


```
const MOD: i64 = 1_000_000_007;

impl Solution {
    pub fn base_unit_conversions(conversions: Vec<Vec<i32>>) -> Vec<i32> {
        let n = conversions.len() + 1;

        // 构建有向树：父节点 -> (子节点, 转换因子)
        let mut children = vec![Vec::new(); n];
        for conv in conversions {
            let (src, dst, factor) = (conv[0] as usize, conv[1] as usize, conv[2] as i64);
            children[src].push((dst, factor));
        }

        // 从根节点 0 开始 DFS，计算每个节点相对于根节点的值
        let mut result = vec![0; n];
        let mut stack = vec![(0usize, 1i64)]; // (节点, 值)
        result[0] = 1;

        while let Some((node, val)) = stack.pop() {
            for &(child, factor) in &children[node] {
                let child_val = (val * factor) % MOD;
                result[child] = child_val as i32;
                stack.push((child, child_val));
            }
        }

        result
    }
}
```
