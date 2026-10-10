---
title: "leetcode-动态规划170"
date: 2026-10-05T20:40:05+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 统计区间内奇妙数的数目

给你两个整数 l 和 r。

如果一个整数的数位形成一个 严格单调 序列，即数位是 严格递增 或 严格递减 的，那么这个整数被称为 好数。所有一位数都被认为是好数。

如果一个整数是好数，或者它的 数位和 是好数，那么这个整数被称为 奇妙数。

返回一个整数，表示在区间 [l, r]（包含边界）内的奇妙数的数量。

如果一个序列中的每个元素都 严格大于 其前一个元素（如果存在），则该序列被称为 严格递增。

如果一个序列中的每个元素都 严格小于 其前一个元素（如果存在），则该序列被称为 严格递减。


```
use std::sync::LazyLock;

/// 判断数位和 `s` 是否为好数（数位严格递增或严格递减）
fn is_good(s: i32) -> bool {
    if s < 100 {
        // 一位数或两位数：十位与个位不相等即为好数
        return s / 10 != s % 10;
    }
    // s 是三位数，其百位一定是 1：只能严格递增
    1 < s / 10 % 10 && s / 10 % 10 < s % 10
}

/// 数位和不是好数的好数（预处理结果，排序后便于二分）
static GOOD_NUMS: LazyLock<Vec<i64>> = LazyLock::new(|| {
    let mut v = Vec::with_capacity(200);
    // 枚举 {0,1,...,9} 的非空子集
    for mask in 1u32..(1 << 10) {
        // 构造严格递减好数（从大到小取子集中的数字）
        let mut x = 0i64;
        let mut sum = 0i32;
        for i in (0..10i64).rev() {
            if mask >> i & 1 == 1 {
                x = x * 10 + i;
                sum += i as i32;
            }
        }
        if !is_good(sum) {
            v.push(x);
        }

        // 构造严格递增好数（必须不含 0）
        if mask & 1 == 1 {
            continue;
        }
        x = 0;
        sum = 0;
        for i in 1..10i64 {
            if mask >> i & 1 == 1 {
                x = x * 10 + i;
                sum += i as i32;
            }
        }
        if !is_good(sum) {
            v.push(x);
        }
    }
    v.sort_unstable();
    v
});

impl Solution {
    pub fn count_fancy(l: i64, r: i64) -> i64 {
        let good_nums = &*GOOD_NUMS;

        // 第 1 部分：统计 [l, r] 中"数位和不是好数"的好数
        let lo = good_nums.partition_point(|&x| x < l);
        let hi = good_nums.partition_point(|&x| x <= r);
        let ans = (hi - lo) as i64;

        // 第 2 部分：统计 [l, r] 中"数位和是好数"的数（不论它本身是否好数）
        let low_s: Vec<i32> = l.to_string().bytes().map(|b| (b - b'0') as i32).collect();
        let high_s: Vec<i32> = r.to_string().bytes().map(|b| (b - b'0') as i32).collect();
        let n = high_s.len();
        let diff_lh = n - low_s.len();

        // memo[i][digit_sum]，-1 表示未计算；数位和最大 9n
        let mut memo = vec![vec![-1i64; n * 9 + 1]; n];

        fn dfs(
            i: usize,
            digit_sum: usize,
            limit_low: bool,
            limit_high: bool,
            low_s: &[i32],
            high_s: &[i32],
            n: usize,
            diff_lh: usize,
            memo: &mut [Vec<i64>],
        ) -> i64 {
            if i == n {
                return is_good(digit_sum as i32) as i64;
            }

            if !limit_low && !limit_high && memo[i][digit_sum] >= 0 {
                return memo[i][digit_sum];
            }

            let lo = if limit_low && i >= diff_lh {
                low_s[i - diff_lh]
            } else {
                0
            };
            let hi = if limit_high { high_s[i] } else { 9 };

            let mut res = 0i64;
            let mut d = lo;

            // 当前处于低位对齐的前导零区间：可以不填数字，且上界不受约束
            if limit_low && i < diff_lh {
                res = dfs(i + 1, 0, true, false, low_s, high_s, n, diff_lh, memo);
                d = 1; // 填数字时从 1 开始，避免前导零
            }

            while d <= hi {
                res += dfs(
                    i + 1,
                    digit_sum + d as usize,
                    limit_low && d == lo,
                    limit_high && d == hi,
                    low_s,
                    high_s,
                    n,
                    diff_lh,
                    memo,
                );
                d += 1;
            }

            if !limit_low && !limit_high {
                memo[i][digit_sum] = res;
            }
            res
        }

        ans + dfs(0, 0, true, true, &low_s, &high_s, n, diff_lh, &mut memo)
    }
}
```
