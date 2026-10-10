---
title: "leetcode-排序6"
date: 2026-10-05T00:34:59+08:00
tags: ["leetcode", "排序"]
draft: false
---


## H 指数

给你一个整数数组 citations ，其中 citations[i] 表示研究者的第 i 篇论文被引用的次数。计算并返回该研究者的 h 指数。

根据维基百科上 h 指数的定义：h 代表“高引用次数” ，一名科研人员的 h 指数 是指他（她）至少发表了 h 篇论文，并且 至少 有 h 篇论文被引用次数大于等于 h 。如果 h 有多种可能的值，h 指数 是其中最大的那个。


```
impl Solution {
    /// 计算研究者的 h 指数
    ///
    /// 思路：h 指数最大不超过论文总数 n，因此可对引用次数做「截断桶计数」：
    /// 引用次数 ≥ n 的论文统一计入桶 n。随后从高到低累加桶中论文数，
    /// 首个满足「累计论文数 ≥ 当前引用次数」的 i 即为最大 h。
    /// 时间 O(n)，额外空间 O(n)。
    pub fn h_index(citations: Vec<i32>) -> i32 {
        let n = citations.len();
        // cnt[i] 表示引用次数恰好为 i 的论文数（i ∈ [0, n-1]；桶 n 收纳 ≥ n 的论文）
        let mut cnt = vec![0usize; n + 1];
        for c in citations {
            cnt[n.min(c as usize)] += 1;
        }

        // 从引用次数高到低累加，找到最大的满足 s >= i 的 i
        let mut s = 0;
        for i in (0..=n).rev() {
            s += cnt[i];
            if s >= i {
                return i as i32;
            }
        }
        unreachable!()
    }
}
```
