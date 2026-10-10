---
title: "leetcode-排序48"
date: 2026-10-05T00:35:02+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 重排数字的最小值

给你一个整数 num 。重排 num 中的各位数字，使其值 最小化 且不含 任何 前导零。

返回不含前导零且值最小的重排数字。

注意，重排各位数字后，num 的符号不会改变。


```
impl Solution {
    /// 重排 num 的各位数字，使其绝对值最小且不含前导零。
    ///
    /// 思路：
    /// - 负数：绝对值越小，原数越大。因此数字按降序排列得到最小绝对值。
    /// - 正数：数字按升序排列，并把最小的非零数字放到首位，其余升序排列。
    /// - 0 直接返回 0。
    pub fn smallest_number(num: i64) -> i64 {
        if num == 0 {
            return 0;
        }

        let is_negative = num < 0;
        let mut x = num.unsigned_abs();

        // 统计每个数字出现次数
        let mut count = [0u32; 10];
        while x > 0 {
            count[(x % 10) as usize] += 1;
            x /= 10;
        }

        let mut res: i64 = 0;

        if is_negative {
            // 负数：数字降序排列，绝对值最小
            for d in (0..=9).rev() {
                for _ in 0..count[d] {
                    res = res * 10 + d as i64;
                }
            }
            -res
        } else {
            // 正数：先放最小的非零数字，再按升序放其余数字
            if let Some(first) = (1..=9).find(|&d| count[d] > 0) {
                count[first] -= 1;
                res = first as i64;
            }
            for d in 0..=9 {
                for _ in 0..count[d] {
                    res = res * 10 + d as i64;
                }
            }
            res
        }
    }
}
```
