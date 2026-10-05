---
title: "leetcode-二分查找29"
date: 2026-09-28T12:29:12+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 最大为 N 的数字组合

给定一个按 非递减顺序 排列的数字数组 digits 。你可以用任意次数 digits[i] 来写的数字。例如，如果 digits = ['1','3','5']，我们可以写数字，如 '13', '551', 和 '1351315'。

返回 可以生成的小于或等于给定整数 n 的正整数的个数 。


```
impl Solution {
    /// 返回用 digits 中的数字（可重复使用）能组成的、小于等于 n 的正整数个数。
    pub fn at_most_n_given_digit_set(digits: Vec<String>, n: i32) -> i32 {
        // 将 digits 转为数字并升序排序
        let mut digits: Vec<i32> = digits.iter().map(|s| s.parse().unwrap()).collect();
        digits.sort_unstable();
        let len = digits.len() as i64;

        // n <= 0 时没有正整数满足条件
        if n <= 0 {
            return 0;
        }

        // 将 n 拆分为数字数组（高位到低位）
        let n_digits: Vec<i32> = n
            .to_string()
            .chars()
            .map(|c| c.to_digit(10).unwrap() as i32)
            .collect();
        let n_len = n_digits.len() as i64;

        let mut ans: i64 = 0;

        // 1. 位数少于 n_len 的数：len^1 + len^2 + ... + len^(n_len-1)
        for i in 1..n_len {
            ans += len.pow(i as u32);
        }

        // 2. 位数等于 n_len 且 ≤ n 的数：逐位确定
        for (i, &cur) in n_digits.iter().enumerate() {
            let remaining = n_len - i as i64 - 1;
            // digits 中小于当前位的数字个数
            let less = digits.partition_point(|&d| d < cur) as i64;
            // 选择这些数字后，剩余位可任意选择
            ans += less * len.pow(remaining as u32);

            // 如果 digits 中没有等于当前位的数字，则无法继续匹配，直接返回
            if digits.binary_search(&cur).is_err() {
                return ans as i32;
            }
        }

        // 所有位都匹配，说明 n 本身也能由 digits 组成
        (ans + 1) as i32
    }
}
```
