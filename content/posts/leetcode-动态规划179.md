---
title: "leetcode-动态规划179"
date: 2026-10-05T20:40:06+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 构造子集和的最少操作次数 II

给你一个整数数组 nums 和一个整数 sum。

一次 操作 中，选择一个当前值为 x 的元素，并将其替换为 2 * x 或 floor(x / 2)。

对于每个元素，乘法 操作和 除法 操作可以按照任意顺序执行。

返回所需的 最少 操作次数，使得操作后的数组中存在一个 子集，其元素之和 恰好 等于 sum。如果无法做到，则返回 -1。

数组的子集是从数组中选择若干个元素得到的集合，也可以不选择任何元素。

floor() 函数返回除法结果的整数部分。


```
impl Solution {
    pub fn min_operations(nums: Vec<i32>, sum: i32) -> i32 {
        const INF: i32 = i32::MAX / 2;
        let sum = sum as usize;

        // f[i]：凑出子集和 i 所需的最少操作次数
        let mut f = vec![INF; sum + 1];
        f[0] = 0;

        for &x in &nums {
            // 生成该元素能变换出的所有可能值，相同值只保留最小操作次数
            // 生成方式：先除 a 次，再乘 b 次，代价 a + b，值 = (x >> a) << b
            let mut costs: Vec<(usize, i32)> = Vec::new();
            let mut a = 0u32;
            while (x as u32) >> a > 0 {
                let base = (x as u32 >> a) as usize;
                let mut b = 0u32;
                // 避免溢出：base << b <= sum
                while (base as u64) << b <= sum as u64 {
                    let v = base << b;
                    let cost = (a + b) as i32;
                    // 相同体积取更小代价
                    match costs.iter_mut().find(|(val, _)| *val == v) {
                        Some(entry) => {
                            if cost < entry.1 {
                                entry.1 = cost;
                            }
                        }
                        None => costs.push((v, cost)),
                    }
                    b += 1;
                }
                a += 1;
            }

            // 按体积升序排序，便于后续提前跳出
            costs.sort_unstable();

            // 0-1 背包：倒序枚举容量
            for i in (1..=sum).rev() {
                for &(v, c) in &costs {
                    if v > i {
                        break;
                    }
                    let cand = f[i - v] + c;
                    if cand < f[i] {
                        f[i] = cand;
                    }
                }
            }

            if f[sum] == 0 {
                return 0;
            }
        }

        if f[sum] == INF { -1 } else { f[sum] }
    }
}
```
