---
title: "leetcode-数学102"
date: 2026-10-07T20:39:35+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 最多有效分割位置 I

给你一个整数数组 nums。

你可以从 nums 中移除 至多一个 元素。记 arr 为按原始顺序保留其余元素后得到的数组，m 为其长度。

如果 arr 的 分割位置 i 满足以下条件，则称其为 有效的 ：

0 <= i < m - 1，且
gcd(arr[0..i]) == gcd(arr[i + 1..m - 1])。

长度为 1 的数组没有有效的分割位置。

arr 的 得分 是其有效分割位置的数量。

返回 arr 的 最大可能得分 。

在这里，gcd(a) 表示数组 a 中所有元素的最大公约数。


```
impl Solution {
    pub fn max_valid_splits(nums: Vec<i32>) -> i32 {
        let n = nums.len();
        let mut ans = Self::count(&nums);

        // 只有当前缀 gcd 发生变化的那些位置，删除后才可能改变结果
        // 收集候选删除下标：0、前缀 gcd 变化点、n-1
        let mut candidates = vec![0];
        let mut pre_gcd = nums[0];
        for i in 1..n - 1 {
            let new_gcd = gcd(nums[i], pre_gcd);
            if new_gcd != pre_gcd {
                candidates.push(i);
                pre_gcd = new_gcd;
            }
        }
        candidates.push(n - 1);

        // 枚举删除候选元素，计算得分取最大值
        let mut new_nums = Vec::with_capacity(n - 1);
        for &i in &candidates {
            new_nums.clear();
            new_nums.extend_from_slice(&nums[..i]);
            new_nums.extend_from_slice(&nums[i + 1..]);
            ans = ans.max(Self::count(&new_nums));
        }

        ans
    }

    // 计算数组的有效分割位置数量
    fn count(nums: &[i32]) -> i32 {
        let n = nums.len();
        if n < 2 {
            return 0;
        }

        // 前缀 gcd 和 后缀 gcd
        let mut pre_gcd = vec![0; n];
        pre_gcd[0] = nums[0];
        for i in 1..n {
            pre_gcd[i] = gcd(nums[i], pre_gcd[i - 1]);
        }

        let mut suf_gcd = vec![0; n];
        suf_gcd[n - 1] = nums[n - 1];
        for i in (0..n - 1).rev() {
            suf_gcd[i] = gcd(nums[i], suf_gcd[i + 1]);
        }

        // 统计满足 pre_gcd[i] == suf_gcd[i + 1] 的分割点
        (0..n - 1).filter(|&i| pre_gcd[i] == suf_gcd[i + 1]).count() as i32
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
