---
title: "leetcode-递归13"
date: 2026-05-26T10:00:17+08:00
tags: ["leetcode", "递归"]
draft: false
---


## 数字 1 的个数

给定一个整数 n，计算所有小于等于 n 的非负整数中数字 1 出现的个数。

```
use std::collections::HashMap;

impl Solution {
    /// 计算从 1 到 n 的所有整数中，数字 1 出现的总次数
    /// 例如：count_digit_one(13) = 6 (1, 10, 11, 12, 13 中共有 6 个 1)
    pub fn count_digit_one(n: i32) -> i32 {
        /// 获取数字 x 的前缀（去掉末位）
        /// 例如：prefix(123) = 12
        fn prefix(x: i32) -> i32 {
            x / 10
        }

        /// 获取数字 x 的末位数字
        /// 例如：last(123) = 3
        fn last(x: i32) -> i32 {
            x % 10
        }

        /// 递归计算 1 到 n 中 1 的个数
        /// 参数：count_1 - 数字到其包含的 1 个数的映射（用于记忆化）
        ///      n      - 当前要计算的上界
        fn f(count_1: &HashMap<i32, i32>, n: i32) -> i32 {
            if 0 == n {
                0  // 基础情况：0 到 0 没有 1
            } else {
                let last = last(n);      // 当前数的末位
                let prefix = prefix(n);   // 当前数的前缀

                match (last, prefix) {
                    // 情况1：末位为 9（如 9, 19, 29, ... 399 等）
                    // 公式：10 * f(prefix) + 1 + prefix
                    // 原因：将 0..n 分成 [0..prefix*10-1] 和 [prefix*10..n]
                    (9, _) => 10 * f(count_1, prefix) + 1 + prefix,

                    // 情况2：n = 0（已经处理过）
                    (0, 0) => 0,

                    // 情况3：n 是一位数且不为 0（如 1-9）
                    // 1 出现 1 次
                    (_, 0) => 1,

                    // 情况4：末位为 0（如 10, 20, 30 等）
                    // 递归计算 n-1，加上前缀中 1 的个数
                    (0, _) => f(count_1, n - 1) + count_1[&prefix],

                    // 情况5：其他情况（末位为 1-8，且前缀不为 0）
                    // 公式：f(n-1-last) + (1+last) * count_1[prefix] + 1
                    _ => f(count_1, n - 1 - last) + (1 + last) * count_1[&prefix] + 1
                }
            }
        }

        /// 构建从 1 到 n 的所有数字的 "包含 1 的个数" 映射表
        /// 键：数字本身，值：该数字的十进制表示中包含 1 的个数
        /// 例如：get_count_1_map(13) 会包含 {1:1, 2:0, 3:0, ..., 9:0, 10:1, 11:2, 12:1, 13:2}
        fn get_count_1_map(n: i32) -> HashMap<i32, i32> {
            /// 递归计算单个数字 n 中包含多少个数字 1
            /// 同时将结果存入 acc 映射表中
            fn count_1(n: i32, acc: &mut HashMap<i32, i32>) -> i32 {
                if 0 != n {
                    // 递归计算前缀中的 1 的个数
                    let count_p = count_1(prefix(n), acc);

                    // 当前数字中 1 的个数 = 前缀中的个数 + (末位是否为 1)
                    let count_c = if 1 == last(n) {
                        1 + count_p  // 末位是 1，加 1
                    } else {
                        count_p      // 末位不是 1，个数不变
                    };

                    // 存储当前数字的结果
                    acc.insert(n, count_c);
                    count_c
                } else {
                    // n = 0 时，没有 1
                    acc.insert(0, 0);
                    0
                }
            }

            let mut count_1_map = HashMap::new();
            count_1(n, &mut count_1_map);  // 递归构建整个映射表
            count_1_map
        }

        // 调用主递归函数，传入映射表和上界 n
        f(&get_count_1_map(n), n)
    }
}
```
