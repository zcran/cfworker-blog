---
title: "leetcode-数论26"
date: 2026-06-20T11:09:55+08:00
tags: ["leetcode", "数论"]
draft: false
---


## 最小化两个数组中的最大值

给你两个数组 arr1 和 arr2 ，它们一开始都是空的。你需要往它们中添加正整数，使它们满足以下条件：

· arr1 包含 uniqueCnt1 个 互不相同 的正整数，每个整数都 不能 被 divisor1 整除 。
· arr2 包含 uniqueCnt2 个 互不相同 的正整数，每个整数都 不能 被 divisor2 整除 。
· arr1 和 arr2 中的元素 互不相同 。

给你 divisor1 ，divisor2 ，uniqueCnt1 和 uniqueCnt2 ，请你返回两个数组中 最大元素 的 最小值 。


```
impl Solution {
    /// 返回两个数组中最大元素的最小值
    ///
    /// # 示例
    /// ```
    /// use solution::Solution;
    /// assert_eq!(Solution::minimize_set(2, 7, 1, 3), 4);
    /// assert_eq!(Solution::minimize_set(3, 5, 2, 1), 3);
    /// assert_eq!(Solution::minimize_set(2, 4, 8, 2), 15);
    /// ```
    pub fn minimize_set(divisor1: i32, divisor2: i32, unique_cnt1: i32, unique_cnt2: i32) -> i32 {
        let total_needed = unique_cnt1 + unique_cnt2;
        let mut left = total_needed;
        let mut right = i32::MAX;

        while left < right {
            let mid = left + (right - left) / 2;

            if can_place(mid, divisor1, divisor2, unique_cnt1, unique_cnt2) {
                right = mid;
            } else {
                left = mid + 1;
            }
        }

        left
    }
}

/// 检查在 [1, v] 范围内是否能放下所有需要的数
fn can_place(v: i32, d1: i32, d2: i32, need1: i32, need2: i32) -> bool {
    let v = v as i64;
    let d1 = d1 as i64;
    let d2 = d2 as i64;
    let need1 = need1 as i64;
    let need2 = need2 as i64;

    // 计算三种类型的数量
    let cnt_d1 = v / d1;              // 被 d1 整除（arr1 不能用）
    let cnt_d2 = v / d2;              // 被 d2 整除（arr2 不能用）
    let cnt_both = v / lcm(d1, d2);   // 同时被 d1 和 d2 整除（都不能用）

    // 去掉同时被两者整除的数
    let only_d1 = cnt_d1 - cnt_both;  // 只能给 arr2（被 d1 整除但不被 d2 整除）
    let only_d2 = cnt_d2 - cnt_both;  // 只能给 arr1（被 d2 整除但不被 d1 整除）
    let can_use = v - cnt_d1 - cnt_d2 + cnt_both; // 两个都能用

    // 检查是否能满足需求
    // arr1 不能使用被 d1 整除的数，所以只能用 only_d2 和 can_use
    // arr2 不能使用被 d2 整除的数，所以只能用 only_d1 和 can_use
    let need_from_shared1 = (need1 - only_d2).max(0);
    let need_from_shared2 = (need2 - only_d1).max(0);

    need_from_shared1 + need_from_shared2 <= can_use
}

/// 计算最小公倍数（先除后乘防溢出）
#[inline]
fn lcm(a: i64, b: i64) -> i64 {
    a / gcd(a, b) * b
}

/// 计算最大公约数（迭代版）
#[inline]
fn gcd(mut a: i64, mut b: i64) -> i64 {
    while b != 0 {
        let temp = b;
        b = a % b;
        a = temp;
    }
    a
}
```
