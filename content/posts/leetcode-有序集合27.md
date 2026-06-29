---
title: "leetcode-有序集合27"
date: 2026-06-14T10:47:42+08:00
tags: ["leetcode", "有序集合"]
draft: false
---


##  最少翻转操作数

给定一个整数 n 和一个整数 p，它们表示一个长度为 n 且除了下标为 p 处是 1 以外，其他所有数都是 0 的数组 arr。同时给定一个整数数组 banned ，它包含数组中的一些限制位置。在 arr 上进行下列操作：

· 如果单个 1 不在 banned 中的位置上，反转大小为 k 的 子数组。

返回一个包含 n 个结果的整数数组 answer，其中第 i 个结果是将 1 放到位置 i 处所需的 最少 翻转操作次数，如果无法放到位置 i 处，此数为 -1 。


```
use std::collections::{VecDeque, BTreeSet};

impl Solution {
    pub fn min_reverse_operations(n: i32, p: i32, banned: Vec<i32>, k: i32) -> Vec<i32> {
        let n = n as usize;
        let p = p as usize;
        let k = k as usize;

        // 标记禁用位置
        let mut is_banned = vec![false; n];
        for &pos in &banned {
            is_banned[pos as usize] = true;
        }

        // 按奇偶性分组存储未访问的位置
        let mut sets = [BTreeSet::new(), BTreeSet::new()];
        for i in 0..n {
            if i != p && !is_banned[i] {
                sets[i & 1].insert(i);
            }
        }

        let mut ans = vec![-1; n];
        let mut queue = VecDeque::from([p]);
        ans[p] = 0;

        while let Some(i) = queue.pop_front() {
            // 计算通过反转长度为 k 的子数组，1 可以从 i 移动到的位置范围
            // 数学推导：位置 i 经过一次反转后可以到达的位置 j 满足：
            // |i - j| <= k-1 且 (i + j - k + 1) 是偶数（保持奇偶性不变）
            let lo = if i >= k - 1 { i - (k - 1) } else { k - 1 - i };
            let hi = if i + k <= n { i + (k - 1) } else { n * 2 - k - 1 - i };

            // 只处理与当前位置奇偶性相同的可达位置
            let set = &mut sets[hi & 1];
            let mut to_remove = Vec::new();

            // 遍历范围内的所有候选位置
            for &j in set.range(lo..=hi) {
                ans[j] = ans[i] + 1;
                queue.push_back(j);
                to_remove.push(j);
            }

            // 批量删除已访问的位置
            for j in to_remove {
                set.remove(&j);
            }
        }

        ans
    }
}
```
