---
title: "leetcode-贪心98"
date: 2026-10-03T12:55:40+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## K 次操作后最大化顶端元素

给你一个下标从 0 开始的整数数组 nums ，它表示一个 堆 ，其中 nums[0] 是堆顶的元素。

每一次操作中，你可以执行以下操作 之一 ：

如果堆非空，那么 删除 堆顶端的元素。
如果存在 1 个或者多个被删除的元素，你可以从它们中选择任何一个，添加 回堆顶，这个元素成为新的堆顶元素。

同时给你一个整数 k ，它表示你总共需要执行操作的次数。

请你返回 恰好 执行 k 次操作以后，堆顶元素的 最大值 。如果执行完 k 次操作以后，堆一定为空，请你返回 -1 。


```
impl Solution {
    pub fn maximum_top(nums: Vec<i32>, k: i32) -> i32 {
        let n = nums.len();
        let k = k as usize;

        // 不操作，堆顶就是初始元素
        if k == 0 {
            return nums[0];
        }

        // 只有一个元素且操作奇数次，堆必为空
        if n == 1 && k % 2 == 1 {
            return -1;
        }

        // 只能操作一次且有多于一个元素时，最优是删掉堆顶，露出 nums[1]
        if k == 1 {
            return nums[1];
        }

        // 核心结论：
        // 删除堆顶后可以把之前删掉的任意元素放回堆顶，
        // 因此经过 k 次操作，堆顶最大值 = 前 min(n, k-1) 个元素中的最大值，
        // 若 k < n，还可以额外考虑 nums[k]（删掉前 k 个后露出的元素）。
        let prefix_max = nums[..n.min(k - 1)].iter().copied().max().unwrap();

        if k < n {
            prefix_max.max(nums[k])
        } else {
            prefix_max
        }
    }
}
```
