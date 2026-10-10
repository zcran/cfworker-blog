---
title: "leetcode-动态规划178"
date: 2026-10-05T20:40:06+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 构造子集和的最少操作次数 I

给你一个整数数组 nums 和一个整数 sum。

一次 操作 中，选择一个当前值为 x 的元素，并将其替换为 2 * x 或 floor(x / 2)。

对于每个元素，对其执行的所有 乘法 操作都必须发生在任何 除法 操作之前。

返回所需的 最少 操作次数，使得操作后的数组中存在一个 子集，其元素之和 恰好 等于 sum。如果无法做到，则返回 -1。

数组的 子集 是从数组中选择若干个元素得到的集合，也可以不选择任何元素。

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
            let w = 32 - (x as u32).leading_zeros(); // x 的二进制长度
            // 0-1 背包：从大到小枚举容量，保证每个元素只用一次
            for i in (1..=sum).rev() {
                // 枚举乘法操作 a 次：x << a
                let mut a = 0u32;
                while ((x as u32) << a) as usize <= i {
                    let v = ((x as u32) << a) as usize;
                    let cost = f[i - v] + a as i32;
                    if cost < f[i] {
                        f[i] = cost;
                    }
                    a += 1;
                }

                // 枚举除法操作 a 次：x >> a（注意 a 从 1 开始，因为 a=0 即原值已由乘法 a=0 覆盖）
                let mut a = w - 1;
                while a > 0 {
                    let v = (x as u32 >> a) as usize;
                    if v > i {
                        break;
                    }
                    let cost = f[i - v] + a as i32;
                    if cost < f[i] {
                        f[i] = cost;
                    }
                    a -= 1;
                }
            }
        }

        if f[sum] == INF { -1 } else { f[sum] }
    }
}
```
