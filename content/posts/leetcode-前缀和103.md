---
title: "leetcode-前缀和103"
date: 2026-08-25T10:02:52+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 使差值相等的最少数组改动次数

给你一个长度为 n 的整数数组 nums ，n 是 偶数 ，同时给你一个整数 k 。

你可以对数组进行一些操作。每次操作中，你可以将数组中 任一 元素替换为 0 到 k 之间的 任一 整数。

执行完所有操作以后，你需要确保最后得到的数组满足以下条件：

存在一个整数 X ，满足对于所有的 (0 <= i < n) 都有 abs(a[i] - a[n - i - 1]) = X 。

请你返回满足以上条件 最少 修改次数。


```
impl Solution {
    pub fn min_changes(nums: Vec<i32>, k: i32) -> i32 {
        let k = k as usize;
        let n = nums.len();
        let half = n / 2;

        // cnt[diff]: 差值 diff 出现的次数
        // cnt2[x]: 需要两次修改的对数，即 max(q, k-p) <= x
        let mut cnt = vec![0; k + 1];
        let mut cnt2 = vec![0; k + 1];

        for i in 0..half {
            let (mut p, mut q) = (nums[i], nums[n - 1 - i]);
            if p > q {
                std::mem::swap(&mut p, &mut q);
            }
            let diff = (q - p) as usize;
            cnt[diff] += 1;

            // 要使得 abs(a[i] - a[n-1-i]) = x，需要修改两个数的最大阈值
            let threshold = q.max(k as i32 - p) as usize;
            cnt2[threshold] += 1;
        }

        let mut ans = n;
        let mut must_change_both = 0; // 必须修改两个数的对数

        for x in 0..=k {
            // 当前差值 = x 的对数不需要修改
            // 其他 half - cnt[x] 对至少修改 1 次
            // 其中 must_change_both 对需要额外再修改 1 次（总共 2 次）
            ans = ans.min(half - cnt[x] + must_change_both);

            // 对于更大的 x，当前这些对都需要修改两个数
            must_change_both += cnt2[x];
        }

        ans as i32
    }
}
```
