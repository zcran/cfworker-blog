---
title: "leetcode-计数100"
date: 2026-08-01T10:40:56+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 所有数对中数位差之和

你有一个数组 nums ，它只包含 正 整数，所有正整数的数位长度都 相同 。

两个整数的 数位差 指的是两个整数 相同 位置上不同数字的数目。

请你返回 nums 中 所有 整数对里，数位差之和。


```
impl Solution {
    pub fn sum_digit_differences(mut nums: Vec<i32>) -> i64 {
        let n = nums.len() as i64;
        let mut res = 0i64;

        // 逐位处理，从最低位到最高位
        while nums[0] > 0 {
            let mut cnt = [0i64; 10];
            for num in nums.iter_mut() {
                cnt[(*num % 10) as usize] += 1;
                *num /= 10;
            }
            // 该位上所有不同数字对的数量
            // 每个出现 c 次的数字，与其他 n-c 个数字形成数位差
            // 每对被计算两次，所以最后统一除以2
            for &c in &cnt {
                res += c * (n - c);
            }
        }

        res / 2
    }
}
```
