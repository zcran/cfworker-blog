---
title: "leetcode-数学103"
date: 2026-10-07T20:39:35+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 最多有效分割位置 II

给你一个整数数组 nums。

你可以从 nums 中移除 至多一个 元素。记 arr 为按原始顺序保留其余元素后得到的数组，m 为其长度。

如果 arr 的 分割位置 i 满足以下条件，则称其为 有效的 ：

0 <= i < m - 1，且
gcd(arr[0..i]) == gcd(arr[i + 1..m - 1])。

长度为 1 的数组没有有效的分割位置。

arr 的 得分 是有效分割位置的数量。

返回 arr 的 最大可能得分 。

gcd(a) 表示数组 a 中所有元素的最大公约数。


```
impl Solution {
    pub fn max_valid_splits(nums: Vec<i32>) -> i32 {
        let n = nums.len();

        // 前缀 gcd：pre_gcd[i] = gcd(nums[0..=i])
        let mut pre_gcd = vec![0; n];
        let mut g = 0;
        for i in 0..n {
            g = gcd(g, nums[i]);
            pre_gcd[i] = g;
        }

        // 后缀 gcd：suf_gcd[i] = gcd(nums[i..])，suf_gcd[n] = 0
        let mut suf_gcd = vec![0; n + 1];
        for i in (0..n).rev() {
            suf_gcd[i] = gcd(suf_gcd[i + 1], nums[i]);
        }

        // 不删除任何元素时的得分
        let all_gcd = suf_gcd[0];
        // [p, n-1] 中 pre_gcd 都是 all_gcd
        let p = pre_gcd.iter().position(|&x| x == all_gcd).unwrap();
        // [0, q] 中 suf_gcd 都是 all_gcd
        let q = suf_gcd[..n].iter().rposition(|&x| x == all_gcd).unwrap();
        // 满足 i >= p 且 i + 1 <= q 的 i 个数
        let mut ans = (q as i32 - p as i32).max(0);

        // 只需枚举前缀 gcd 发生变化的删除位置
        for i in 0..n {
            if i > 0 && pre_gcd[i] == pre_gcd[i - 1] {
                continue;
            }

            // 删除 nums[i] 后的左右两侧 gcd
            let pre_g = if i > 0 { pre_gcd[i - 1] } else { 0 };
            let suf_g = if i < n - 1 { suf_gcd[i + 1] } else { 0 };

            // 判断删除 nums[i] 后新数组的整体 gcd 是否发生变化
            // 只有 new_g 严格大于 all_gcd 时，得分才可能提升
            let new_g = if suf_g > all_gcd && (pre_g == 0 || pre_g % suf_g == 0) {
                suf_g
            } else if pre_g > all_gcd && (suf_g == 0 || suf_g % pre_g == 0) {
                pre_g
            } else {
                continue;
            };

            // 从左找第一个使前缀 gcd == new_g 的位置 p
            let mut p = 0;
            g = 0;
            for j in 0..n {
                if j == i {
                    continue;
                }
                g = gcd(g, nums[j]);
                if g == new_g {
                    p = j;
                    break;
                }
            }

            // 从右找第一个使后缀 gcd == new_g 的位置 q
            let mut q = 0;
            g = 0;
            for j in (0..n).rev() {
                if j == i {
                    continue;
                }
                g = gcd(g, nums[j]);
                if g == new_g {
                    q = j;
                    break;
                }
            }

            // 新数组得分 = q - p（若删除位置处于分割点范围内则减 1）
            let mut res = q as i32 - p as i32;
            if p <= i && i < q {
                res -= 1;
            }
            ans = ans.max(res);
            break;
        }

        ans
    }
}

// 迭代版欧几里得算法求最大公约数
fn gcd(mut a: i32, mut b: i32) -> i32 {
    while b != 0 {
        let t = a % b;
        a = b;
        b = t;
    }
    a
}
```
