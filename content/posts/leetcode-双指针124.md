---
title: "leetcode-双指针124"
date: 2026-09-05T09:53:08+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 将数字变成二进制回文数的最少操作

给你一个整数数组 nums。

对于每个元素 nums[i]，你可以执行以下操作 任意 次（包括零次）：

将 nums[i] 加 1，或者
将 nums[i] 减 1。

如果一个数的二进制表示（不包含前导零）正读和反读都一样，则称该数为 二进制回文数。

你的任务是返回一个整数数组 ans，其中 ans[i] 表示将 nums[i] 转换为 二进制回文数 所需的 最小 操作次数。


```
use std::sync::OnceLock;

/// 预计算所有 <= 5000 的二进制回文数，并附加哨兵 5049（大于 5000 的第一个二进制回文数）。
fn palindromes() -> &'static Vec<i32> {
    static PALINDROMES: OnceLock<Vec<i32>> = OnceLock::new();
    PALINDROMES.get_or_init(|| {
        const MX: i32 = 5000;
        const BASE: i32 = 2;

        let mut pal = vec![0]; // 哨兵：0 也是回文数

        let mut pw = 1;
        loop {
            // 生成奇数长度回文数：以 i 为中心，镜像除最低位外的部分
            for i in pw..pw * BASE {
                let mut x = i as i64;
                let mut t = i / BASE;
                while t > 0 {
                    x = x * BASE as i64 + (t % BASE) as i64;
                    t /= BASE;
                }
                if x > MX as i64 {
                    pal.push(5049); // 哨兵
                    return pal;
                }
                pal.push(x as i32);
            }

            // 生成偶数长度回文数：完全镜像 i
            for i in pw..pw * BASE {
                let mut x = i as i64;
                let mut t = i;
                while t > 0 {
                    x = x * BASE as i64 + (t % BASE) as i64;
                    t /= BASE;
                }
                if x > MX as i64 {
                    pal.push(5049); // 哨兵
                    return pal;
                }
                pal.push(x as i32);
            }

            pw *= BASE;
        }
    })
}

impl Solution {
    pub fn min_operations(nums: Vec<i32>) -> Vec<i32> {
        let pal = palindromes();
        nums.into_iter()
            .map(|x| {
                // 二分找到第一个 >= x 的位置
                let j = pal.partition_point(|&v| v < x);
                // 比较变成 >= x 的回文数或 < x 的回文数所需的操作次数
                (pal[j] - x).min(x - pal[j - 1])
            })
            .collect()
    }
}
```
