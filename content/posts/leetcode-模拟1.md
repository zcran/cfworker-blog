---
title: "leetcode-模拟1"
date: 2026-08-08T11:31:08+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 字符串相乘

给定两个以字符串形式表示的非负整数 num1 和 num2，返回 num1 和 num2 的乘积，它们的乘积也表示为字符串形式。

注意：不能使用任何内置的 BigInteger 库或直接将输入转换为整数。


```
impl Solution {
    /// 字符串相乘：以字符串形式返回两个非负整数的乘积。
    ///
    /// 算法：竖式乘法（Grade-school multiplication）。
    /// 1. 用一个数组 res 累加每一位的乘积和；
    /// 2. 从低位到高位统一处理进位；
    /// 3. 跳过前导零后输出结果。
    pub fn multiply(num1: String, num2: String) -> String {
        // 任一数为 "0" 则直接返回
        if num1 == "0" || num2 == "0" {
            return "0".to_string();
        }

        let m = num1.len();
        let n = num2.len();
        let mut res = vec![0u32; m + n];
        let b1 = num1.as_bytes();
        let b2 = num2.as_bytes();

        // 累加乘积：num1[i] * num2[j] 的结果落在 res[i+j+1]
        for i in 0..m {
            let d1 = (b1[i] - b'0') as u32;
            for j in 0..n {
                res[i + j + 1] += d1 * (b2[j] - b'0') as u32;
            }
        }

        // 统一处理进位（从右向左）
        for i in (1..res.len()).rev() {
            res[i - 1] += res[i] / 10;
            res[i] %= 10;
        }

        // 跳过前导零，转换为字符串
        let start = res.iter().position(|&d| d != 0).unwrap_or(res.len() - 1);
        res[start..].iter().map(|&d| (d as u8 + b'0') as char).collect()
    }
}
```
